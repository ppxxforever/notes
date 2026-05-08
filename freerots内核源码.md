## 1.列表
状态切换
![image.png](https://notes-1361987228.cos.ap-beijing.myqcloud.com/20260506214555284.png)

```c
PRIVILEGED_DATA static List_t pxReadyTasksLists[ configMAX_PRIORITIES ];
PRIVILEGED_DATA static List_t xDelayedTaskList1;
PRIVILEGED_DATA static List_t xDelayedTaskList2; 
PRIVILEGED_DATA static List_t * volatile pxDelayedTaskList; //指向正在使用的
PRIVILEGED_DATA static List_t * volatile pxOverflowDelayedTaskList; //溢出的指针
PRIVILEGED_DATA static List_t xPendingReadyList;
```
freertos通过列表管理任务，这三种状态对应三种列表

> [!NOTE]
> 为啥有两个Delay列表：
> 为处理 tick 计数溢出 FreeRTOS 的延时按“解除阻塞时间（xItemValue）”排序，当 xTickCount 回绕时，原本“更晚”的时间会变成更小。为避免排序混乱，把延时任务分两表：当前计数未溢出的放 pxDelayedTaskList，溢出的放pxOverflowDelayedTaskList。当 xTickCount 回绕时，两表交换角色，逻辑保持一致

```c 
typedef struct tskTaskControlBlock 
{
	volatile StackType_t * pxTopOfStack; //栈顶指针
	ListItem_t xStateListItem; //状态
	ListItem_t xEventListItem; //事件
	UBaseType_t uxPriority; //优先级
	StackType_t * pxStack; //栈
	char pcTaskName[ configMAX_TASK_NAME_LEN ];
} tskTCB;
```
栈用来还原现场
xStateListItem：表示任务当前的“状态”，通过挂在不同的列表来体现。
xEventListItem：用于任务等待事件的场景（信号量、队列、事件组）。
```c
struct xLIST_ITEM
{
	listFIRST_LIST_ITEM_INTEGRITY_CHECK_VALUE
	configLIST_VOLATILE TickType_t xItemValue; //优先级
	struct xLIST_ITEM * configLIST_VOLATILE pxNext;
	struct xLIST_ITEM * configLIST_VOLATILE pxPrevious; 
	void * pvOwner;//指向tcb的指针
	struct xLIST * configLIST_VOLATILE pxContainer;//当前所在的状态列表
	listSECOND_LIST_ITEM_INTEGRITY_CHECK_VALUE 
};
typedef struct xLIST_ITEM ListItem_t;
```

```c
typedef struct xLIST
{
	listFIRST_LIST_INTEGRITY_CHECK_VALUE 
	configLIST_VOLATILE UBaseType_t uxNumberOfItems;//tcb的数量
	ListItem_t * configLIST_VOLATILE pxIndex;//指向当前任务的指针
	MiniListItem_t xListEnd;//表示尾部方便遍历和插入，不指向任务
	listSECOND_LIST_INTEGRITY_CHECK_VALUE 
} List_t;
```
### 1.2 列表的初始化
```c
void vListInitialise( List_t * const pxList )
{
	pxList->pxIndex = ( ListItem_t * ) &( pxList->xListEnd );
	pxList->xListEnd.xItemValue = portMAX_DELAY;
	pxList->xListEnd.pxNext = ( ListItem_t * ) &( pxList->xListEnd );
	pxList->xListEnd.pxPrevious = ( ListItem_t * ) &( pxList->xListEnd );
	pxList->uxNumberOfItems = ( UBaseType_t ) 0U;
}
```
接受一个列表头，然后很明显，初始化，由于当前列表没有任务所以uxNumberOfItems=0
xListEnd 指向自己
### 1.3 任务的切换
