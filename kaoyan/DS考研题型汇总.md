--------------

## 目录

<!-- GMF-TOC -->

* [一、线性表](#一线性表)
  * [1.1线性表的合并问题](##11线性表的合并问题)
    * [1.1.1两个递增单链表合并成递减单链表](####111两个递增单链表合并成递减单链表)
    * [Q2.两个递增单链表合并成递增单链表](####q2两个递增单链表合并成递增单链表)
    * [Q3.一个递增单链表和一个递减单链表哦合并成一个递增单链表](####q3一个递增单链表和一个递减单链表哦合并成一个递增单链表)
    * [Q4.两个循环单链表合并成一个循环单链表](####两个循环单链表合并成一个循环单链表)
  * [1.2线性表的集合类操作问题](##1-2线性表的集合类操作问题)
    * [Q1.求两个递增单链表的元素的交集](####q1求两个递增单链表的元素的交集)
    * [Q2.求两个递增单链表的元素的并集](####q2求两个递增单链表的元素的并集)
  * [1.3线性表的排序问题](##1-3线性表的排序问题)
  * [1.4线性表的重复值问题](##1-4线性表的重复值问题)

<!-- GFM-TOC -->



# 一、线性表

## 1.1线性表的合并问题

#### 1.1.1两个递增单链表合并成递减单链表

```cpp
//假设la和lb带头节点，因为是采用头插法，因此小的元素会被优先插入
void Merge_Desc(List* &la, List* &lb){
    List *tmp, *p = la->next, *q = lb->next;	//p和q分别为la和lb的工作指针
    la->next = NULL;	//最后的结果需要保存到la中，因此先把la清理出来
    while(p && q){
        if(p->data <= q->data){
            tmp = p->next;	//tmp暂存p的后继指针
            p->next = la->next;	
            la->next = p;	//头插法插入小节点
            p = tmp;		//la的工作指针继续后移
        }else{
            tmp = q->next;	//tmp暂存q的后继指针
            q->next = la->next;
            la->next = q;	//头插法插入b的节点
            q = tmp;		//lb的工作指针q继续后移
        }
    }
    //到这一步肯定有一个链表访问完了
    if(p)
    	q = p;	//两个链表长度不相等的时候，处理非空的那个链表
    while(q){
        tmp = q->next;
        q->next = la->next;
        la->next = q;
        q = tmp;
    }
    free(lb);
}
```

#### Q2.两个递增单链表合并成递增单链表

```cpp
//假设la和lb带头节点，采用的是尾插法，小的元素会被优先插入
void Merge_Asc(List* &la, List* &lb){
    List *tmp, *p = la->next, *q = lb->next;	//p和q分别为la和lb的工作指针
    la->next = NULL;	//最后的结果需要保存到la中，因此先把la清理出来
    List *tail = la;	//tail表示最后链表的尾节点所在位置
    while(p && q){
        if(p->data <= q->data){
            tmp = p->next;	//保存la的下一个工作指针的位置
            p->next = NULL;	//将p从原来的la中断开
            tail->next = p;	//直接把p连接到la的尾部
            tail = tail->next;	//同时更新tail
            p = tmp;		//la的工作指针继续后移
        }else{
            tmp = q->next;	//保存lb的下一个工作指针的位置
            q->next = NULL;	//将q从原来的lb的中断开
            tail->next = q;	//将q链接到la的我尾部
            tail = tail->next;	//更新tail
            q = tmp;		//lb的工作指针继续后移
        }
    }
    //到这一步肯定有一个链表访问完了
    if(p){	//如果la还没访问完
		tail->next = p;	//直接把剩余的原来的la连接到新的la尾部    
    }else{
        tail->next = q;	//同理如果lb没有访问完，链接剩下的lb到新的la尾部
    }
}
```

#### Q4.一个递增单链表和一个递减单链表哦合并成一个递增单链表

```cpp
//假设la和lb带头节点，要求用O(m+n)的时间复杂度，假设la递增，lb递减
void Merge_Diverse(List* &la, List* &lb){
    //首先先把lb逆序成递增
    List *p = la->next, *q = lb->next, *tmp;
    while(q){
        tmp = q->next;
        q->next = lb->next;
        lb->next = q;
        q = tmp;
    }
    Merge_Asc(la, lb);	//转化为将两个递增单链表合并成递增单链表
}
```

#### Q4.两个循环单链表合并成一个循环单链表

```cpp
//两个带头节点的循环单链表la和lb，以及他们的长度m和n，用最快的速度合并
void Merge_Cycle(List* &la, List* &lb， int m, int n){
    List *p = la->next, *q = lb->next;
    List *ta, *tb;
    //找到最短的节点的尾节点，插入到另一个链表的头部
    if(m < n){	//la比lb短
        while(p != la->next)
            p = p->next;
        tb = lb->next;	//保存lb的第一个节点
        lb->next = la->next;	//将la头插到lb
        p->next = tb;	//将lb第一个节点链接到la的尾部
    }else{	//lb短
        while(q != lb->next)
            q = q->next;
        ta = la->next;
        la->next = lb->next;
        q->next = ta;
    }
}
```

## 1.2线性表的集合类操作问题

#### Q1.求两个递增单链表的元素的交集

```cpp
//假设la和lb均带头节点，将所求交集保存到la中
void List_Inter(List* &la, List* &lb){
    List *p = la->next, *q = lb->next, *prev;	//prev用来保存已求交集的最后一个元素，用来判重
    List *na, *nb;	//na和nb用来保存下一个节点的
    la->next = prev = NULL;	//把la清理出来
    while(p && q){
        if(p->data < q->data){ //说明p的值小了，需要让p前移来找和q相等的节点
            while(p->data < q->data)
                p = p->next;
            if(p->data > q->data){	//p移过了
                q = q->next;	//只能把q往后移，进行下一个循环
                continue;
            }
        }else if(p->data > q->data){	//说明q的值笑了，需要让q前移来找和p相等的点
            while(p->data > q->data)
                q = q->next;
            if(p->data < q->data){	//q移过了
                p = p->next;	//只能把p往后移，进行下一个循环
                continue;
            }
        }
        //此时说明p和q相等，
        na = p->next;	//保存当前la工作指针的下一个指针位置
        p->next = NULL;	//把p提取出来
        if(prev == NULL){	//说明是第一次插入新的la
            la->next = p;
            prev = p;	//更新prev
        }else{
            prev->next = p;
            prev = p;
        }
        p = na;			//p继续更新
        q = q->next;	//q继续更新
    }
    //la或者lb有一个访问完了
    if(p){	//如果la没有访问完
        while(p){
            if(p->data == prev->data)
                continue;	//重了，继续前进
            na = p->next;
            p->next = NULL;
            prev->next = p;
            prev = p;
            p = na;
        }
    }else{	//如果lb没有访问完
        while(q){
            if(q->data == prev->data)
                continue;
            nb = q->next;
            q->next = NULL;
            prev->next = q;
            prev = q;
            q = nb;
        }
    }
}
```

#### Q2. 求两个递增单链表的元素的并集

```cpp
void List_Union(List* &la, List* &lb){
    List *p = la->next, *q = lb->next;
    List *na, *nb;	//na和nb用来保存下一个指针的地址
    List *prev = NULL;	//prev用来判重，表示union的最后一个元素
    la->next = NULL;	//把la清理出来
    while(p && q){
        if(p->data < q->data){
            if(prev == NULL){	//新的la的第一个元素
                na = p->next;
                p->next = NULL;
                la->next = p;
                prev = p;
                p = na;
            }else{	//已经有元素了
                if(prev->data == p->data)
                    continue;	//元素重复，直接忽略
                na = p->next;
                p->next = NULL;
                prev->next = p;
                prev = p;
                p = na;
            }
        }else{
            if(prev == NULL){
                nb = q->next;
                q->next = NULL;
                lb->next = q;
                prev = q;
                q = nb;
            }else{
                if(prev->data == q->data)
                    continue;
                nb = q->next;
                q->next == NULL;
                prev->next = q;
                prev = q;
                q = nb;
            }
        }
    }
    //la和lb有一个访问完了
    if(p){
        while(p){
            if(prev->data == p->data)
                continue;
            na = p->next;
            p->next = NULL;
            prev->next = p;
            prev = p;
            p = na;
        }
    }else{
        while(q){
            if(prev->data == q->data)
                continue;
            nb = q->next;
            q->next == NULL;
            prev->next = q;
            prev = q;
            q = nb;
        }
    }
}
```



