# ArrayList和LinkedList
## 一、ArrayList
> ArrayList实现了List接口，是个顺序容器，底层是以数组实现的。接下来我们分析其核心方法：构造函数、add、get。
- 构造函数
```java

    // 核心属性 用来存储元素
    transient Object[] elementData;

    private static final Object[] EMPTY_ELEMENTDATA = {};

    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    // 1. 空构造函数 初始化时为一个空数组
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
    // 2. 自定义数组容量
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } 
        // fail-fast 小于0 抛出异常
        else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
    // 将一个集合转化成ArrayList来存储
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }

```
- add
```java

    public boolean add(E e) {
        // 数组容量不足时进行扩容
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        // 将元素赋值到index=size下，并且size自增1
        elementData[size++] = e;
        return true;
    }
    private void ensureCapacityInternal(int minCapacity) {
        // 如果是第一次添加元素，采用默认容量DEFAULT_CAPACITY=10
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        ensureExplicitCapacity(minCapacity);
    }
    private void ensureExplicitCapacity(int minCapacity) {
        // 修改该字段记录修改次数 iterator会用到该字段进行fail-fast操作 抛出异常ConcurrentModificationExceptions
        modCount++;
        // 容量不足进行扩容
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
    private void grow(int minCapacity) {
        int oldCapacity = elementData.length;
        // 先以1.5倍进行扩容
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        // 如果扩容溢出了就以入参为准
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        // 如果比最大数组长度还大就采用Integer.MAX_VALUE
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // 进行数组扩容
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

```
- get
```java

    public E get(int index) {
        // 如果数组越界直接抛出异常IndexOutOfBoundsException
        rangeCheck(index);
        // 返回数组下标为index的元素
        return elementData(index);
    }
    private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

```

## 二、LinkedList
> linkedList底层是以双向链表存储的。
> linkedList中的每个元素都是一个Node对象，并且持有了前置节点prev和后置节点的引用。当是第一个节点时，prev是null，当是最后一个节点是next是null。Node结构如下：
```java

    private static class Node<E> {
        E item; // 当前元素内容
        Node<E> next; // 后置节点
        Node<E> prev; // 前置节点

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }

```
- add 核心发放是linkLast
```java

  void linkLast(E e) {
        // 拿到尾巴节点
        final Node<E> l = last;
        // 新生成一个节点，prev指向尾巴节点，next指向null
        final Node<E> newNode = new Node<>(l, e, null);
        // 修改尾巴节点的引用为新生成的节点
        last = newNode;
        // 如果之前的尾巴节点是空，说明未添加过元素，则新节点作为头节点；否则新节点放在老尾巴节点的后面
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }

```
- get
```java

    public E get(int index) {
        // 校验index是否有效
        checkElementIndex(index);
        // 寻找index的Node
        return node(index).item;
    }
    Node<E> node(int index) {
        // 如果index小于元素数量的一半，则从前向后迭代；否则从后向前迭代
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }

```

## 三、总结
1. ArrayList和LinkedList都实现了List的接口，但是其底层数据结构不一样，前者是数组，后者是双向链表。
2. 针对随机访问，ArrayList效率更高，因为数组可以根据index直接访问，而链表还需要遍历。
3. 一般采用ArrayList，但是如果要用到双端队列等特性，就要用LinkedList了。