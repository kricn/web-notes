## 队列(Queue)
队列和栈(Stack)的读写数据方式不同，队列是遵循先入先出(FIFO)原则\
就像排队一样，谁排在前面，谁就第一个完成。
## 队列实现
```javascript
class Queue {
  constructor () {
    this.queue = []
  }
  //入队
  enqueue = val => {
    this.queue.push(val)
  }
  //出队
  dequeue = () => {
    this.queue.shift()
  }
  //队头
  head = () => {
    return this.queue[0]
  }
  //队尾
  tail = () => {
    return this.queue[this.queue.length - 1]
  }
  //序列化
  toString = () => {
    return this.queue.join(",")
  }
  //判空
  empty = () => {
    return this.queue.length === 0
  }
  //长度
  size = () => {
    return this.queue.length
  }
  //清空
  clear = () => {
    this.queue = []
  }
}
```
## 优先队列
既然队列像排队一样，那就可以插队:pushpin:\
给加入队列的成员加上优先级lv，控制其在队列的位置，这里lv越大，排得越前
```javascript
//修改enqueue方法如下
//默认lv为1
enqueue = (val, lv=1) => {
  if(this.empty()){
    this.queue.push([val, lv])
  } else {
    for (let i = 0; i < this.size(); i ++) {
      if ( lv > this.queue[i][1] ) {
        //大于队列中的优先级就排其前面
        this.queue.splice(i, 0, [val, lv])
        break
      } else if (lv === 1) {
        //默认优先级则直接派最后
        this.queue.push([val, lv])
        break
      }
    }
  }
}
let queue = new Queue()
queue.enqueue("a-lv1")
queue.enqueue("b-lv2", 2)
queue.enqueue("c-lv3", 3)
queue.enqueue("d-lv3", 3)
queue.enqueue("e-lv4", 4)
queue.enqueue("f-lv1")
console.log(queue.queue)
/*
  [
    ["e-lv4", 4],
    ["c-lv3", 3],
    ["d-lv3", 3],
    ["b-lv2", 2],
    ["a-lv1", 1],
    ["f-lv1", 1],
  ]
*/
```
## 循环队列
队列成环即循环队列，如，一条队列里，第x个人出列（或其他条件），直到队列剩余1人（若其它条件），
队列需要循环多次达到目的即循环序列
```javascript
const circularQueue = (queue, tail) => {
  //队列为空停止循环，也可以放其他条件
  while(!queue.empty()) {
    for (let i = 0; i < tail - 1; i ++) {
      //因为队列只能排在头部的元素才能出列
      //将不满足的头部元素出列，再重新排到队列末尾，形成循环
      queue.enqueue(queue.dequeue())
    }
    //循环一次后，排在第一个的即是满足条件的元素，出列
    console.log(queue.dequeue() + "出列")
  }
}
```
## 用队列实现基数排序
基数排序就是给写一个整数数组，先对其个位进行排序，并送入相应下标的队列中，各位排完序后，再按下标顺序出列，
接着再去排十位，百位...\
借用菜鸟教程一张图解释
![](https://www.runoob.com/wp-content/uploads/2019/03/radixSort.gif)
```javascript
let queues = []  //存储下标的队列，比如其个位是1，那么就放进queues[1]中
let nums = []    //需要排序的数组
function distribution(queues, nums) {
  //找出最大数
  const max = Math.max(...nums)
  //最大数是几位数
  const maxLen = String(max).length

  //开始循环，循环次数是最大数的长度（几位数）
  for (let i = 0; i < maxLen; i ++) {
    //t用来标志nums里的数字，每循环完一个位数就归零（因为此时该位数已经排好了）
    let t = 0
    //循环nums
    for (let j = 0; j < nums.length; j ++) {
      //这里是从个位开始排序
      if ( i === 0 ) {
        queues[ nums[j] % 10 ].enqueue(nums[j])
      } else {
        //获取除各位外的其它位数
        queues[ Math.floor( (nums[j] / Math.pow(10, i)) % 10 ) ].enqueue(nums[j])
      }
    }
    //每排序好一个位数，重新收集一次数组，下次再次排序
    for (let k = 0; k < 10; k ++) {
      while  ( !queues[k].empty() ) {
        nums[t++] = queues[k].dequeue()
      }
    }
  }
}
```

