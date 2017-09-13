## JS基础
`return (x % 2 == 0) ? true : false` 也可以写成`return (x % 2== 0)`

假如有这样一个数组:
```
var names =['Ana', 'ana', 'john', 'John'];
console.log(names.sort()); 
```
`["Ana", "John", "ana", "john"]`

既然a在字母表里排第一位，为何ana却排在了John之后呢?这是因为JavaScript在做字符比较 的时候，是根据字符对应的`ASCII`值来比较的。例如，A、J、a、j对应的`ASCII`值分别是65、75、 7、106。

## 栈 STACK
LIFO 堆成一堆的书

选择用数组来保存栈里的元素

+ push(element(s)):添加一个(或几个)新元素到栈顶。
+ pop():移除栈顶的元素，同时返回被移除的元素。
+ peek():返回栈顶的元素，不对栈做任何修改(这个方法不会移除栈顶的元素，仅仅返回它)。
+ isEmpty():如果栈里没有任何元素就返回true，否则返回false。
+ clear():移除栈里的所有元素。
+ size():返回栈里的元素个数。这个方法和数组的length属性很类似。

```
function Stack() {
    var items = [];
    this.push = function(element){
        items.push(element);
    };
    this.pop = function(){
        return items.pop();
    };
    this.peek = function(){
        return items[items.length-1];
    };
    this.isEmpty = function(){
        return items.length == 0;
    };
    this.size = function(){
        return items.length;
    };
    this.clear = function(){
        items = [];
    };
    this.print = function(){
        console.log(items.toString());
    }; 
}
```

转换进制
```
function baseConverter(decNumber, base) {

    var remStack = new Stack(),
        rem,
        baseString = '',
        digits = '0123456789ABCDEF';

    while (decNumber > 0) {
        rem = Math.floor(decNumber % base);
        remStack.push(rem);
        decNumber = Math.floor(decNumber / base);
    }

    while (!remStack.isEmpty()) {
        baseString += digits[remStack.pop()];
    }

    return baseString;
}
```

## 队列 QUEUE
FIFO

+ enqueue(element(s)):向队列尾部添加一个(或多个)新的项。
+ dequeue():移除队列的第一(即排在队列最前面的)项，并返回被移除的元素。
+ front():返回队列中第一个元素——最先被添加，也将是最先被移除的元素。队列不
做任何变动(不移除元素，只返回元素信息——与Stack类的peek方法非常类似)。
+ isEmpty():如果队列中不包含任何元素，返回true，否则返回false。
+ size():返回队列包含的元素个数，与数组的length属性类似。

```
function Queue() {
    var items = [];
    this.enqueue = function(element){
        items.push(element);
    };
    this.dequeue = function(){
        return items.shift();
    };
    this.front = function(){
        return items[0];
    };
    this.isEmpty = function(){
        return items.length == 0;
    };
    this.clear = function(){
        items = [];
    };
    this.size = function(){
        return items.length;
    };
    this.print = function() {
    };
}
```

优先队列
```
function PriorityQueue() {
    var items = [];
    function QueueElement (element, priority) { 
        this.element = element;
        this.priority = priority;
    }
    this.enqueue = function(element, priority) {
        var queueElement = new QueueElement(element, priority);
        if (this.isEmpty()) {
            items.push(queueElement);
        } else {
            var added = false;
            for (var i=0; i<items.length; i++){
                 if (queueElement.priority < items[i].priority) {
                    items.splice(i,0,queueElement); 
                    added = true;
                    break; 
                } 
            }
        if (!added) { 
            items.push(queueElement);
        } 
    }
//其他方法和默认的Queue实现相同 
}
```
循环队列，击鼓传花
```
function hotPotato (nameList, num) {
    var queue = new Queue();
    for (var i=0; i<nameList.length; i++) {
        queue.enqueue(nameList[i]); 
    }
    var eliminated = '';
    while (queue.size() > 1) {
        for (var i=0; i<num; i++) {
            queue.enqueue(queue.dequeue()); 
        }
        eliminated = queue.dequeue();
        console.log(eliminated + '在击鼓传花游戏中被淘汰。');
    }
    return queue.dequeue();
}
var names = ['John','Jack','Camila','Ingrid','Carl']; 
var winner = hotPotato(names, 7); 
console.log('胜利者:' + winner);
```

## 链表
> 链表存储有序的元素集合，但不同于数组，链表中的元素在内存中并不是连续放置的。每个 元素由一个存储元素本身的节点和一个指向下一个元素的引用(也称指针或链接)组成。

链表的一个好处在于，添加或移除元素的时候不需要移动其他元素。数组可以直接访问任何位置的任何元素，而访问链表中的一个元素，需要从起点开始迭代列表直到找到所需的元素。

+ append(element):向列表尾部添加一个新的项。
+ insert(position, element):向列表的特定位置插入一个新的项。
+ remove(element):从列表中移除一项。
+ indexOf(element):返回元素在列表中的索引。如果列表中没有该元素则返回-1。
+ removeAt(position):从列表的特定位置移除一项。
+ isEmpty():如果链表中不包含任何元素，返回true，如果链表长度大于0则返回false。 
+ size():返回链表包含的元素个数。与数组的length属性类似。 
+ toString():由于列表项使用了Node类，就需要重写继承自JavaScript对象默认的
toString方法，让其只输出元素的值。

## 集合 Set
> 集合是由一组无序且唯一(即不能重复)的项组成的。

+ add(value):向集合添加一个新的项。
+ remove(value):从集合移除一个值。
 has(value):如果值在集合中，返回true，否则返回false。
+ clear():移除集合中的所有项。
+ size():返回集合所包含元素的数量。与数组的length属性类似。  
+ values():返回一个包含集合中所有值的数组。

## 字典 Dictionary

+ set(key,value):向字典中添加新元素。
+ remove(key):通过使用键值来从字典中移除键值对应的数据值。
+ has(key):如果某个键值存在于这个字典中，则返回true，反之则返回false。 
+ get(key):通过键值查找特定的数值并返回。
+ clear():将这个字典中的所有元素全部删除。
+ size():返回字典所包含元素的数量。与数组的length属性类似。
+ keys():将字典所包含的所有键名以数组形式返回。
+ values():将字典所包含的所有数值以数组形式返回。

### 散列表 Hashtable 也叫 Hashmap
Hashmap是Dictionary类的一种散列表实现方式

> 散列算法的作用是尽可能快地在数据结构中找到一个值。散列函数的作用是给定一个键值，然后 返回值在表中的地址。

## 树