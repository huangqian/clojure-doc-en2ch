#Clojure程序流程控制.

# Clojure程序流程控制 #

## 函数： ##
> 作为一个函数式编程语言，每一个Clojure 程序全是由函数组成。Clojure 的代码为树形结构，每一个函数作为一个树的节点分支出来，并调用其他函数。了解Clojure就意味着要了解它的函数和模式。如果粗心的编码你的程序很容易变得像碗杂酱面一样，混乱难懂。所以仔仔细细的编码才能使你的代码变得更快，更优雅，而你也将更享受的去写和读它们。

### “一等公民” 函数 ###
> 在Clojure里，所有的函数都是“一等公民”，一切都是基于函数的，意味着如下几条：
    1. 可以在程序运行时的任何时刻被动态的创建加载；
    1. 没有固定的命名，但是可以被绑定到若干个标识符；
    1. 可以以任何数据结构进行存储；
    1. 可以在函数之间被传递，传递到其他函数中进行运算；
相对而言其他静态语言的函数来说，比如Java、C。在这些语言中，函数总是需要在编译之前定义和命名。Clojure有这个极大的优势，包括其他的函数式编程语言，它们都可以在任何时刻定义一个新的函数，以及以任何数据结构进行存储。

### 用fn定义函数 ###
> 创建函数最基本的方式就是用fn专用语句块，在求值后返回一个新的函数。在最简单的块中，参数符号的集合（Vector 一个由方括号括起来的列表）以及函数体。

---

**note** ：Vector 由方括号括起来的集合，尚未讨论它，第四章有关于Vector其他特性的详细介绍，现在你可以先把它当成一个表达列表（list）的替换方式。不像列表（list）那样用圆括号括起来，求值时没有指向一个函数调用，所以它比较合适的去表达文本数据结构、没有多的函数调用，更快、更简单。

---

在REPL上的一个例子，你可以定义一个很简单的函数，提供两数相乘的功能。
```
user=> (fn [x y] (* x y))
```
> 这个form可能看起来稍微复杂，但是它确实很简单：它只是另外三个form组成的：fn、`[x y]`、(`*` x y)。fn就是调用其他两个form的，`[x y]`是参数集合，说明这个新的函数有两个参数：x、y。(`*` x y)则是函数体，x、y分别绑定到各自的参数。`*`是乘法的运算符，Clojure的运算符都在前面。函数里面没有显示的“return”返回语句，因为函数总会返回表达式的求值结果。
然后而这是没多大用的，它只返回返回这个函数，而且这个函数也被转换成要在REPL中打印的字符串，这个字符串看起来完全没什么用。
```
#<user$eval__43$fn__45 user$eval__43$fn__45@ac06d4>
```
而且，这个函数你也现在也不能使用，因为你没有将这个函数绑定到任何标识符上面或者你没有将它存储在任何数据类型上。jvm可能立即会将它当垃圾回收了，因为它没有更多的使用。
典型的使用是要将它绑定到一个var上：
```
user=> (def my-mult (fn [x y] (* x y)))
```
现在你就可以在任何你有权限的上下文使用它了。
```
user=> (my-mult 3 4)
12
```
而且，他的工作原理是公开的。表达式(fn `[x y]` (`*` x y))求出一个第一类对象函数，这个函数被绑定到my-mult上面。调用my-mult，你就可以用第一个元素“my-mult” 来执行函数求出一个list。“my-mult”当作这个新的函数，“3”和“4”被当作参数传入。

注意，将function分配到symbol不是唯一使用它的方式，只要将这个function放到一个form的第一个元素位置，对这个form求值的时候function就会被执行，不管这个function是不是绑定到一个symbol或者其他的东西上。举个例子，这个例子证明将function的定义和使用在同一个form里面是完全可行的：
```
user=> ((fn [x y] (* x y)) 3 4) 
12
```
在这个form里面，值得注意的是函数的定义(fn `[x y]` (`*` x y))放在form的第一个元素，
求值时(fn `[x y]` (`*` x y))被解析为一个function，“3 4”被当成参数传递给它。跟调用一个绑定了function的symbol一样。
总得来说跟js差不多。
这里要强调提醒一下，function与它绑定到的那个symbol是不能画等号的，在前面的例子，my-mult等于(fn `[x y]` (`*` x y))，只能理解为 (fn `[x y]` (`*` x y)) 绑定到my-mult这个符号上面。当它被调用时，通过解析它获得一个function然后接着去调用这个function。

### 用defn定义函数 ###
尽管function与他们绑定的符号并不是同一个东西，但是通过绑定和命名函数是迄今为止使用函数最常见的方法。为此，Clojure提供了一个定义函数并绑定到符号的快捷方法：**defn**。
**defn**的字面意思是**def**和**fn**的合并，但是更简短、方便。它还提供了一个对函数添加文档字符串的功能，可以通过文档介绍这个函数如何使用。
**defn**接着往后的参数分别是：一个符号名，一个文档字符串（可选的），一个参数列表（vector），一个函数体的表达式（expression）。举个例子，下面的代码定义了一个函数，求一个参数的平方：
```
user=> (defn sq 
  "Squares the provided argument" 
  [x] 
         (* x x)) 
```
然后，你就可以使用制定的函数名来调用该函数：
```
user=> (sq 5) 
25 
```
你可以通过内置函数**doc**来查看函数的文档。系统输出会打印出该函数的文档信息。
```
user=> (doc sq) 
--------------------- 
user/sq 
([x]) 
   Squares the provided argument 
nil 
```

---

**tip**:**doc** 函数在编程的时候是非常有用的，Clojure所有的内置函数都有很好的文档（包括所有的类库），而且在**REPL**上使用**doc**函数也是相当方便的。同样，在你自己编写函数的时候也要养成给函数添加文档的习惯，及时没有其他人回来看你的代码，因为在一两周之后它对你自己的记忆也是有很大帮助的，让你正确的记得你当时定义这个函数是做什么的。

---

### 多元函数 ###
元数是指函数的参数可以有多少组的组数，（类似与java的方法重载，调用方法时通过传递给它们的不同参数个数和参数类型来决定具体使用哪个方法，多态性。）在Clojure中也可以基于元数来定义多个预备的实现。
这里同样使用**fn**和**defn**来定义函数，只是在参数上面有一些轻微的修改。之前是传递一个参数的vector和一个函数体的表达式（expression），现在要传递多个vector/expression对用圆括号括起来。下面代码比较好解释：
```
user=> (defn square-or-multiply 
         "squares a single argument, multiplies two arguments" 
        ([] 0) 
        ([x] (* x x)) 
        ([x y] (* x y))) 
```
这里定义的函数square-or-multiply有三个预定的实现。第一个的参数是一个空的vector，不传参数时被调用，返回常量0.第二个实现带了一个参数，返回该参数的平方值。第三个实现有两个参数，返回值为这两个参数的乘积。在REPL中验证：
```
user=> (square-or-multiply) 
0 
user=>(square-or-multiply 5) 
25 
user=>(square-or-multiply 5 2) 
10 
```
### 可变参数函数 ###
经常需要一些函数的参数个数是不定的，被叫做可变参函数（variable arity）。Clojure满足了这个要求，通过定义函数时使用特殊符号`&` 放在参数vector里面实现。**fn**和**defn**都可以使用。
使用：定义参数vector时，只需将`&` 再加上一个符号名放在普通参数后面即可。函数调用时任何附加的参数都会被装在一个序列（seq，类似于list）的结构传递进去。然后这个序列将被绑定在参数列表`&`后面的那个符号上面。用下面的代码举个例子：
```
user=> (defn add-arg-count 
         "Returns the first argument + the number of additional arguments" 
        [first & more] 
        (+ first (count more))) 
```
**count**是一个简单的内置函数，它返回一个list的长度。查看运行结果：
```
user=> (add-arg-count 5) 
5 
user=> (add-arg-count 5 5) 
6 
user=> (add-arg-count 5 5 5 5 5 5) 
10 
```
第一次的调用，只有一个参数5，它被绑定到**first**上面而且有个空的list绑定到**more**上，因为没有附加的参数，'(count more)' 返回0。所以返回值也为第一个参数的值。然而，第二、三次调用时**more**分别绑定到了'(5)'和'(5 5 5 5 5)'上，长度分别为1和5，再加上第一个参数5并返回和。
第四章讨论了list以及一些通用函数来解析和求出他们的值。他们都能对绑定了list的**more**使用。
### 快捷声明函数（Shorthand Function Declaration） ###
用**fn**定义函数本来就很简洁，但是有时候完整输入的时候还是很麻烦的。特别是有时候只是内部声明一个函数，并没有绑定到全局变量上。
Clojure提供了一个定义函数的快捷形式，form里面有个一个阅读器宏（Reader macro）。用快捷方式定义一个函数：前面一个**#** 后面跟上一个表达式，这个表达式作为function的函数体。以及直接在这个表达式里面用百分号作为function的参数。

---

**note** Reader macros是专业的，通用的快捷语法。因为它在Clojure里面只有唯一的形式，没有被圆括号、中括号、花括号包起来。Clojure代码解析时最先解析他们，在代码编译之前把他们还原成正常形式。在编译器看来快捷形式的函数#(`*` `%`1 `%`2)等同于长形式的(fn `[x y]` (`*` x y))。阅读器宏提供了几个非常常见的任务，而且不能被用户自定义。此限制背后的理由是，读者宏的过度使用，使代码无法阅读，除非读者非常熟悉宏上面的问题。防止用户自定义阅读器宏有助于降低共享代码和保持Clojure语言一致性的难度。尽管如此，它还是对某些常见的form很有用的，所以Clojure默认提供了一个小的集合。

---

举个例子,下面是用shorthand的方式实现的一个求平方的函数：
```
user=> (def sq #(* % %)) 
#'user/sq 
user=> (sq 5) 
25 
```
这里的百分号意味着这个函数有一个参数，绑定到函数体中。如果定义多个参数的函数，只需在百分号后面加上1到20的数字即可：
```
user=> (def multiply #(* %1 %2)) 
'#user/multiply 
user=> (multiply 5 3) 
15 
```
**%1** 或者**%**代表第一个参数，**%2**代表第二个参数，以此类推。这样很容易看出快捷函数定义是多么的简洁，特别是内部函数：
```
user=> (#(* % %) 5) 
25 
```
## 条件表达式（Conditional Expressions） ##
任何编程语言都应该有在根据情况来改变程序流程的基本特性，Clojure当然也不例外，它提供了全套简单的条件形式。
最基本的条件形式是**if**语句，将一个判断表达式作为它的第一个参数进行求值。如果求值为true，那么就返回它的第二个参数（相当于“then”子句）的求值结果。如果结果为false（包括nil）就返回第三个参数的求值结果（相当于“else”子句），前提是有提供第三个参数并且不为空。用下面的代码举个例子：
```
user=> (if (= 1 1) 
     "Math still works.") 
"Math still works." 
```
另外一个例子执行else表达式：
```
user=> (if (= 1 2) 
     "Math is broken!" 
     "Math still works.") 
"Math still works." 
```
Clojure也提供了**if-not**语句。跟**if**的用法相同，但是作用是相反的。当逻辑为false的时候会去计算第二个参数的值，为true的时候才计算第三个参数的值。
```
user=> (if-not (= 1 1) 
     "Math is broken!" 
   "Math still works.") 
"Math still works." 
```
有时候，逻辑不仅仅只有true和false，而有多个选项时。你可能会使用嵌套的**if**，但是这里有个更简洁的形式**cond**。**cond**可以有任意个“判断/表达式”对，作为它的参数。如果满足第一个判断，就执行第一个判断对应的表达式。如果没有满足第一个条件，就会尝试后面的判断表达式，以此类推。如果一个都没有满足，那么返回**nil**除非你用一个**:else**关键字放在最后来抓住剩下的所有可能性。举个例子，让我们用**cond**来定义一个评论天气的function：
```
(defn weather-judge 
"Given a temperature in degrees centigrade, comments on the weather." 
[temp] 
(cond 
    (< temp 20) "It's cold" 
    (> temp 25) "It's hot" 
:else  "It's comfortable")) 
```
用下面的代码试试：
```
user=> (weather-judge 15) 
"It's cold" 
user=> (weather-judge 22) 
"It's comfortable" 
user=> (weather-judge 30) 
"It's hot" 
```

---

**Tip**  **cond**本来是很有用，但是大量的使用它可能会导致代码难以维护，尤其是你程序中可能出现的情况在不断的增长。其实对这种情况的更优处理方式，用**multimethods**实现多态调用，更简单，扩展性更强。在第九章有详细的讲解。

---


## 局部变量绑定 ##
在函数式编程语言中，新的值都是从嵌套多个函数调用的函数组合那儿获得的。但是有时为计算返回的值指派一个名称也是很有必要的，这样可以是代码更清晰，使其复用，提高效率。
Clojure提供了**let**形式可以达到这样的效果。**let**还允许你绑定多个变量，并将这些绑定的符号放到一个主体表达式中进行使用。这些符号的作用域也只能在**let**语句内，而且不可变，一旦这些符号被绑定整个**let**语句内得到的都是相同的值。
**let** 形式有一个包含了若干“名称-值”对的vector，以及一个主表达式，举个例子，下面的**let**表达式 将a绑定到2，b到3然后求它们的和：
```
user=> (let [a 2 b 3] (+ a b)) 
5 
```
这是最简单的方式使用**let**，然而，**let**会导致代码更琐碎，比起它提供的价值它更能带来复杂度。举一个更能引起注意的使用**let**的例子。考虑下面的代码：
```
(defn seconds-to-weeks 
"Converts seconds to weeks" 
[seconds] 
 (/ (/ (/ (/ seconds 60) 60) 24) 7)) 
```
它工作正常，但是读起来不是条理不是很清晰。嵌套的调用除法函数有些混乱。尽管大多数人不需太费心就能理解这些代码的意思。但是看起来简单的代码会少做更多的工作，并且，别人在也可以通过一些不是很熟悉的值和操作想象出类似的函数。函数要是写成这样也行永远也没有人能搞懂它。
我们可以使用**let**来让函数定义更清晰：
```
(defn seconds-to-weeks 
"Converts seconds to weeks" 
[seconds] 
(let [minutes (/ seconds 60) 
       hours (/ minutes 60) 
       days (/ hours 24) 
       weeks (/ days 7)] 
 weeks)) 
```
代码变长了，但是你可以清楚的知道每一个计算步骤都是干什么的，你将每一步的值都帮到一个中间符号，minutes、hours、days然后在返回weeks，而不是一气呵成的把整个计算合到一步完成，这个例子展示了一个通常的代码风格的选择。它使代码更清晰，也是代码变得更长了。什么时候，何时以及怎么使用都按照你自己的意愿。但是原则就是简单：**let**会使你的代码更清晰，而且将计算的返回值存到一个变量里面，可以增加代码的复用。

### 循环和递归(Looping and Recursion) ###

这对习惯于命令式编程语言的用户会有一些小小的冲击，因为Clojure没有提供直接的循环语法。然而，像其他函数式编程语言一样，它在遇到需要多次执行相同代码的情景时用递归来代替。因为Clojure鼓励使用不可变的数据结构，递归提供了一个更好的概念比典型的循环更适合，强制迭代。
从命令式编程语言进入函数式编程语言最大的挑战就是递归思想。但它却有令人惊讶的强大和优雅，而且你会很快的学会如何轻松的用递归来表达任何重复计算。
大多数程序员对递归都有个最简单的概念--那就是函数自己调用自己，虽然这个也不错，但是有关递归的作用，以及如何有效的使用它与它各个不同的场景是如何工作的都没有进行更深入的了解。
在Clojure（或者其他任何函数式编程语言，关于这一点）中有效的使用递归，只需将这些指南记住就可以了：
  1. 使用一个递归函数的参数，来存储和修改计算进度。在命令式编程语言中，循环需要依赖重复修改一个变量。但是在Clojure中没有可以修改的变量。取而代之的是充分利用函数的参数，不要认为递归会重复修改任何东西。但是最为一个函数调用链，每次调用需要包含继续计算所需的所有信息。在一个递归计算过程中的任何值或修改结果应作为参数传递到下一个的递归函数调用，因此它就可以持续运行。
  1. 确保递归有一个基本情况或基本条件。在每个递归函数中需要有个条件判断，如果某些目标或者判断条件已经达到或者满足，就得停止递归并返回结果。类似于命令式编程，防止无限循环。如果没有情况使代码停止，那它永远也不会停。显然，这会导致很多问题。
  1. 每次迭代，递归都必须向目标条件靠近一步。否则，就不能保证它什么时候能结束。通常情况下，通过判断一些或大或小的数值是否达到某个临界值来作为基本条件。
举个例子，用牛顿算法来递归计算任何数的平方根。这个Clojure小程序有一个主函数以及几个辅助函数，展示了递归的功能。

_列表3-1计算平方根_
```
(defn abs 
    "Calculates the absolute value of a number" 
    [n] 
    (if (< n 0) 
        (* -1 n) 
        n)) 
 
(defn avg 
    "returns the average of two arguments" 
    [a b] 
    (/ (+ a b) 2)) 
 
(defn good-enough? 
    "Tests if a guess is close enough to the real square root" 
    [number guess] 
    (let [diff (- (* guess guess) number)] 
        (if (< (abs diff) 0.001) 
            true 
            false))) 
 
(defn sqrt 
    "returns the square root of the supplied number" 
    ([number] (sqrt number 1.0)) 
    ([number guess] 
    (if (good-enough? number guess) 
        guess 
        (sqrt number (avg guess (/ number guess)))))) 
```
让我们试试，将这个文件载入Clojure的运行环境后，然后在REPL中执行：
```
user=> (sqrt 25) 
5.000023178253949 
user=> (sqrt 10000) 
100.00000025490743 
```
众所周知，上面的代码返回一个数的平方根，精度在0.001以内。
前面三个函数不用过于追究，都是简单的辅助函数，除非你想去看看。重点在第四个函数**sqrt**上。
**sqrt**最明显的东西是有两个实现。第一个实现可以想成是一个public接口。很容易的调用，只需要一个参数：你想求它平方根的数。第二个是递归的实现，传两个参数，包括你认为最可能的猜测。第一个实现只不过是用了一个初始值1.0在调用第二个实现。
递归的的实现很简单，第一步先用定义的good-enough?函数检查判定基本条件，如果你的猜测足够接近实际的平方根则返回true。如果基本条件得到满足，那就不用重复执行了。只需将猜测作为答案放回。
如果没有个满足基本条件就必须继续调用函数自己完成递归。将一个平方根的猜测和这个数字作为参数传递进来，这些参数都是它计算时需要用到的。这符合上述递归函数定义的第一个特点。
最后，记录表达式返回的猜测值提供给下一次迭代：` (avg guess (/ number guess))`.
它每次都传递猜测值与平方值除以目前猜测值的商加起来的平均值。这个数学性质保证每次返回的值都比上一次的值更接近平方根。这符合一个良好的递归函数的最后一个要求。每次迭代它都使进度和获取的值都更接近于正确结果。最后它保证结果满足 good-enough?并返回结果停止计算。
另外一个例子：列表3-2是一个用递归计算指数的函数：
```
(defn power 
    "Calculates a number to the power of a provided exponent." 
    [number exponent] 
    (if (zero? exponent) 
        1 
        (* number (power number (- exponent 1))))) 
```
用下面的代码试试：
```
user=> (pow 5 3) 
125 
```
这个函数用递归的方式与求平方根的函数不一样。这里用的数学描述是x<sup>n = x `*` x</sup>(n-1),这个函数返回的是一个数乘以它的n-1次幂等于这个数的n次幂。你还有个基本情况：如果指数为0则返回1，因为任何数的0次方的1。然后因为你每次都对指数减去1，那么最后你就会计算到0次方（除非你传入了一个复指数）。这样函数总会向满足基本条件的方向发展。

---

**Note**当然，还有比上面定义的函数实现求平方根和幂的方法，它们都在java的标准数学类库，可以非常简单的从Clojure中调用。这里只是为了展示一下递归的逻辑。在基于Java互操作性那章有详细介绍如何调用Java库。

---


## 尾递归（Tail Recursion） ##

关于递归存在一个实际的问题，因为物理计算机的硬件限制，导致内联函数的个数也会受到限制（栈空间的大小有限）。在JVM里面，这样会变得相当大

### Clojure的尾递归（Clojure’s recur） ###

在一些函数式编程语言中，比如Scheme，递归调用在函数尾部时自动完成最佳优化。但是Clojure则不是这样，尾递归必须显式的使用**recur**形式。
当你需要使用尾递归的时候，只需将尾调用时的函数名换成**recur**即可。这样它就会使用包含这个尾部调用的函数的最优化设置。
举个例子，列表3-3 是一个非递归函数，求出小于给出数的的所有数的和。
例如： (add-up 3) = 1 + 2 + 3 = 6.
```
(defn add-up 
    "adds all the numbers below a given limit" 
    ([limit] (add-up limit 0 0 )) 
    ([limit current sum] 
        (if (< limit current) 
          sum 
          (add-up limit (+ 1 current) (+ current sum))))) 
```
它可以通过递归的规则正常工作，它将到当前为止所有数的总和，以及界限值已参数传递到下一个迭代。然后每次迭代都会判断一个基本情况（当前数字是不是已经大于界限值），以及都每次迭代都会使当前数字越来越靠近界限值。这样的函数对处理当数字不大的时候是没有问题的：
```
user=> (add-up 3) 
6 
user=> (add-up 500)
125250
```
但是你用它去计算一个很大的数的话，它就会挂掉：
```
user=> (add-up 5000) 
java.lang.StackOverflowError
```
现在你就需要使用尾递归优化了，重新定义一下函数，直接用**recur**替换掉**adds-up**来调用就行了，就像清单3-4一样。
清单3-4，正确使用用尾递归添加数字
```
(defn add-up 
    "adds all the numbers up to a limit" 
    ([limit] (add-up limit 0 0 )) 
    ([limit current sum] 
        (if (< limit current) 
          sum 
          (recur limit (+ 1 current) (+ current sum)))))
```
现在你可以再试试：
```
user=> (add-up 5000) 
12502500
```
使用**recur**，运行无问题，无论有多少递归它都可以顺利执行，只要你愿意等。
Note：Clojure因为有些地方没有默认的使用尾递归优化而遭受抨击。但是有些情况也是有可能不需要用**recur**。尽管**recur**这个发明限制了JVM自动尾递归优化，但是很多Clojure社区成员发现，显示的使用尾递归比隐式的使用个清晰和方便。在Clojure中，你可以很容易地告诉别人这个函数有没有使用尾递归，而且还可以防止出现一个错误。如果有些地方使用**recur**，它会保证程序在递归过程中不会出现内存溢出。如果你在不合适的地方使用尾递归，编译器会报出信息。这样你就再也不用去考虑什么地方该用尾递归什么地方不该用。

### 使用循环（Using loop） ###
特殊形式**loop**，**loop**与**recur**结合使用，当定义一个函数并立即要使用的时候，**loop**会更简单。理论上，**loop**跟匿名函数的递归是没什么区别的，但是它使得匿名函数的递归更容易阅读，可以看到它是怎样循环的，其实尾递归也是一样。
用**loop**形式定义一个循环体。依次有两个形式：第一个，一个初始化参数绑定的vector（name/value 的形式）,第二个就是函数体的表达式，每当**recur**在里面被使用时，它就会递归调用这个循环，并且将任何传递的参数重新绑定到之前定义的参数名上。
举个例子，下面是一个非常简单的循环，初始化将0与符号i绑定，每次迭代都加上一个增量，当i等于10就返回。
```
(loop [i 0] 
    (if (= i 10) 
        i 
        (recur (+ i 1))))
```
记住一点，像任何这样的递归函数，函数体内就必须有一个基本条件（when i = 10），而且每一次迭代都必须使进程向基本条件靠近。与递归函数不同，当没有任何需求去定义一个函数时，**loop**可以设立一个函数，并初始化参数，而且提供一个**recur**递归时回调点。你同样可以看作一个递归调用。
当**recur**与**loop**结合使用的时候，有一点是非常有用的。在其他函数语言中有个很普遍的习惯，那就是写递归函数的时候函数体内一般会有两个实现，一个是递归，一个不是。
典型的情况：不是递归的那个一般是用于初始化值，并调用递归。这是良好递归风格的自然结果——递归函数可能需要多个参数来保持跟踪它的计算状态，但是又不需要总是去调用这个函数本身。**loop**提供的功能能使其变得更简洁。来看一个例子，这是从前面的求平方根的函数演变过来的（用**recur**替换了直接的递归调用）
```
(defn sqrt 
    "returns the square root of the supplied number" 
    ([number] (sqrt number 1.0)) 
    ([number guess] 
    (if (good-enough? number guess) 
        guess 
        (recur number (avg guess (/ number guess))))))
```
注意这个函数的两个实现——没有递归的版本用来初始化**guess**的值，然后再启用递归。
其实你可以用**loop**来重构着两步，只需一步：
```
(defn loop-sqrt 
    "returns the square root of the supplied number" 
    [number] 
    (loop [guess 1.0] 
        (if (good-enough? number guess) 
            guess 
            (recur (avg guess (/ number guess))))))
```
这个版本的函数就只有一个实现了，**loop**一设置**guess**的初始值就立刻执行其函数体。当**recur**被调用时，函数又会调用loop语句，不会调用整个函数的顶层。传到**recur**里面的参数都在**loop**里面匹配好，所以每次迭代的**guess**都已经在**loop**绑定了新值。这以为着代码的循环部分整洁的包装在**loop**与**recur**之间。


## 函数式编程技术(Functional Programming Techniques) ##

前面讲述了在Clojure程序中如何声明函数和程序控制流程的基本结构，这些只是基础，由此可以构建出最基本的程序组件。大部分Clojure标准库的内容都是以这种基本结构实现的（包括在12章上讨论的基于宏（macro）的异常处理结构）。
然而， 为了写出好的Clojure程序， 你不仅要知道这些基本结构， 也必须知道高效地使用这些基本结构技术， 同时还要了解Clojure允许你做的方方面面。 大部分这些技术都不是Clojure独有的， 这是所有函数式编程语言共有的特征。

### 作为一等（基本）对象（结构）的函数(First-Class Functions) ###

函数可以被求值， 可以被（作为参数）传递给其他函数， 可以（作为返回值）从其他函数返回。 这是函数式语言的一个重要特征。 它绝不是用代码来耍点小聪明， 而是实现结构化程序的关键。 通过将功能模块（作为参数）传递给函数， 就有可能写出极其泛化和几乎消除重复的代码。
使用作为一等对象的函数主要是两方面的问题： 1.作为参数传递给函数并在函数内部被调用， 2.在函数内创建一个函数并作为结果返回。 前者从概念上讲很简单，也是相对普通的使用方式， 而后者则功能更为强大。

### 一等对象 函数的使用(Consuming First-Class Functions) ###

函数可以将其他函数作为参数就是所谓的“高阶函数”。 大部分的“系列操作”的库（在第五章中讨论）就是基于这项技术。
允许函数将其他函数作为参数， 主要是为了让函数的功能更为泛化。 通过将特定的更为具体的行为委托给作为参数的函数， 外层函数可以更为通用， 因此也适合在更广阔的场景中使用。
比方说， 在下面的例子中， 函数arg-switch用来计算另一函数应用在两个参数上的结果和将两参数换序后再应用该函数的结果。 关键是要注意， 作为参数的函数可以是任意带两个参数的函数， 可能你在设计这个函数的时候心里只想过一个函数， 但其他的函数一样没问题：
```
(defn arg-switch
	"Applies the supplied function to the arguments in both possible orders."
	[fun arg1 arg2]
	(list (fun arg1 arg2) (fun arg2 arg1)))
```
此函数arg-switch会产生一个有两项元素的列表, 第一项是参数为原始顺序时调用参数函数的结果，第二项是参数换序后调用参数函数的结果 。在REPL中测试一下：
```
user=>(arg-switch / 2 3)
(2/3 3/2)
```
在此，你传递三个参数给arg-switch:做除法的函数，数字2，数字3。返回结果是有两项的列表：第一项是2/3，第二项是3/2。两项都是使用分数的形式， 这是Clojure表示比例数（有理数）的默认方式。
如果传递其他的函数给arg-switch也没问题：
```
user=>(arg-switch > 2 3)
(false true)
```
当传递一个“大于”函数的时候， 返回结果是(false true)，分别是(> 2 3)和(> 3 2)的结果。当然非数值函数也没问题，你可以用字符串连接函数来试试：
```
user=>(arg-switch str "Hello" "World")
("HelloWorld" "WorldHello")
```
你甚至可以传递一个自定义的内联函数（也是匿名函数）:
```
user=>(arg-switch (fn [a b] (/ a (* b b))) 2 3)
(2/9 3/4)
```
如你所见， 通过允许在函数中使用其他函数作为参数， 在创建一个可以在各种场景（假设你一开始就需要这么一组功能）中使用的非常宽泛灵活的函数的时候， 你不需要做其他额外的工作了。相较为了每一种不同的操作而不断重复的代码， 通过使用一等函数来定义函数拥有说不尽的好处。当程序变得更加复杂之后， 这更是一种优势。 函数可以完全关注自身的核心逻辑， 而把非核心逻辑委托给其他的操作。

### 产生作为一等对象的函数(Producing First-Class Functions) ###

函数不仅可以作为其他函数的参数， 也可以构建函数并将其作为值返回。但如果不保持代码的整洁和可读性的话， 代码可能就难以理解了， 但是这仍然是一个很强悍的功能。
这也是Lisp历史上长期和人工智能联系在一起的主要原因， 人们认为函数创建其他函数有可能让机器来进化和定义机器行为。 虽然自修正程序从来就没有如人们期望的那样实现过， 但在运行时定义函数的能力对很多日常的程序任务来讲还是很强悍很有用的。

举例说明， 下面是一个简单的函数，它创建并返回一个检测一个数是否在指定范围内的函数：
```
(defn rangechecker
	"Returns a function that determines if a number is in a provided range."
	[min max]
	(fn [num]
		(and (<= num max)
			(<= min num))))
```
如果要使用返回的函数， 可以在REPL中调用上面的函数并保存返回结果：
```
user=> (def myrange (rangechecker 5 10))
#’user/myrange
```
然后就可以像调用其他函数一样调用新的函数myrange了
```
user=> (myrange 7)
true
user=> (myrange 11)
false
```
如果你只是需要一次范围检查， 直接定义一个可能更为简单， 但如果在一个程序中有很多动态生成的范围或者有上千个不同的范围需要检查， 那么创建一个rangechecker这样的函数工厂还是非常有用的。 对于功能比范围检查更复杂的函数来讲， 这是一个很大的成功了， 因为对于那些可以动态生成的函数而言， 都没有必要去手写一堆的复杂逻辑了。

### 闭包(Closures) ###

许多人关注Clojure这个名字， 认为闭包closure是Clojure的主要特征，但， 啥是闭包？ 为什么人们这么关心这个？
简短地讲，闭包是包含值和代码的一等函数， 这些值在函数范围内声明， 并随着函数存在而一直存在。函数一声明，本地（局部）值就被绑定到函数引用的符号变量， 并随着函数一起保存。闭包封闭（因此叫闭包）在函数之中并被函数维护。 这意味着， 闭包内的值在函数的整个生命期内都是有效的， 同时， 函数也可以是闭包。

比方说， 前面的rangechecker函数实际上就是一个闭包， 内层函数引用了min, max两个符号变量， 如果这两个值没有被封闭并且不是内层函数的一个有效的部分， 那内层函数在被调用的时候， 这些变量就在内层函数的范围之外了。 因此， 生成的函数会一直持有这些符号变量， 那生成的函数在被调用的时候这些符号变量也就是有效的了。

一旦函数被创建， 那被封闭的值也就无法修改了， 所以， 实际上这些值已经成为函数内部的常量了。

闭包的一个有趣的性质就是它的二重性----行为和数据----它们可以实现面向对象语言中对象的某些任务。就像java中使用拥有一个方法的匿名内部类来模拟一等函数， 闭包也可以被视为只有一个方法的对象。 如果你把这个方法实现为向闭包传递消息的派发器， 那这就是一个全对象系统的开始了（虽然对大部分程序来讲这一点杀伤力太大）。
（上面这一段话， 不怎么理解）对于那些数据和行为同样重要的函数来讲， 创建成闭包是很常见的事。
### 局部套用和组合函数(Currying and Composing Functions) ###
局部套用----由Moses Schonfinkel首次发明，但是由Haskell Curry命名----指的是把一个函数转化成另一个函数----该函数通过把参数包装到闭包中而减少参数数目----的过程。 以这种方式操作函数是很有用的， 因为这允许创建新的定制的函数而不需要一个个明确的定义。
### 使用partial实现局部应用(Using partial to Curry Functions) ###
在Clojure中， 任何函数通过使用partial函数都可以被局部调用， partial函数接受一个函数作为其他第一个参数， 任意个其他的附加参数， 返回一个和所提供的函数功能相似的函数， 但这个函数需要更少的参数， 它使用partial作为那额外的参数。
比方说， 乘法函数`*`通常至少带两个参数才有用， 但如果你需要一个单参数的版本， 你可以使用partial----绑定一个特定的值创建一个单参数函数----来适应你的需求：
```
user=> (def times-pi (partial * 3.14159))
#’user/times-pi
```
现在你就可以来用一个参数来调用times-pi了，也就是会把参数乘以PI:
```
user=> (times-pi 2)
6.28318
```
注意(times-pi 2)和(**3.14159 2)是完全相同的。你所做的只是创建了一个部分参数已经定义的乘法而已。 通过定义函数你也可以完成同样的功能：
```
(defn times-pi
“Multiplies a number by PI”
	[n]
	(* 3.14159 n))
```
虽然这有些烦琐， 但这个函数无非是向乘法函数提供一个特定的值并做相应的包装。 在此就可以看到局部应用了：没有必要明确的写出这个简单的包装函数。partial返回的函数与我们自己定义的times-pi函数其实是一样的， 但是通过使用partial函数， 你可以明确的知道，times-pi函数就是对应了乘法函数和一个特定的值。 这样， 代码就更容易被跟踪， 而且也更精确的反映了抽象的逻辑。**

### 使用comp实现组合应用(Using comp to Compose Functions) ###

与局部应用结合在一起的另一个很强悍的工具就是函数的组合应用。 设想在某个场景中， 如果所以的函数在定义时都需要使用到其他的函数， 那么， 这每一个函数就是一个composition， 但是， 我们也可以通过使用comp函数来组合已有的函数， 而不需要在函数体中去明确指出要用到哪些函数。
comp带任意个函数作为参数， 返回一个从右到左依次调用传入函数的函数， 从调用最右边的函数开始， 将结果传给左边的函数， 依次执行下去。 因此， comp返回的这个函数和作为参数的最右边的函数具有相同数量的参数， 除了最右边这个函数， 其他的所有函数都必须使用单个（不是零个也不是多个）参数。 最终这个函数的返回值， 也就是最左边这个函数的返回值。
来点实际的， 考虑下面在REPL中这个例子：
```
user=> (def my-fn (comp - *))
#'user/my-fn
```
这样就定义了一个函数my-fn，该函数带任意数量的参数， 先把这些参数相乘， 然后取反， 最后返回这个结果， 试一下:
```
user=> (my-fn 5 3)
-15
```
正如我们希望看到的一样， 结果是-(5 `*` 3)，或者说-15。分析一下执行过程：首先， 用所给的参数3和5调用最右边函数， 在这里就是乘法函数了， 返回15， 然后把15传给取反函数， 得到-15， 因为这已经是最左边的函数了， 所以这个取反函数的返回值就是my-fn的返回值。 你在此可以使用comp，是因为my-fn的执行逻辑可以被展开成依次执行乘法和取反。 当然， 其实我们也可以手写一个函数完成相同的功能：
```
(defn my-fn
	“Returns –(x * y)”
	[x y]
	(- (* x y)))
```
但是， 因为my-fn其实没干什么有意义的事， 只是组合了乘法和取反这两个函数， 所以， 使用comp更简单， 也更具表现力一些。

因为传给comp的函数（除最右边外）都要求带单一参数， 这也使得它们可能通过partial实现局部调用。比方说， 你需要一个和上面差不多的函数， 但要求把最终返回的结果再乘以10， 大概就是这样的一个数学表达式10`*`-(x`*`y)。
通常情况下， 只使用comp是搞不定的----除了最右边的函数，其他函数都得带单个参数， 而乘法是要求至少两个参数的。 但是通过把partial后的结果传给comp作为参数， 你就可以绕开这个限制了：
```
user=> (def my-fn (comp (partial * 10) - *))
#'user/my-fn
user=> (my-fn 5 3)
-150
```
果然如我们所愿， 分析一下执行过程：第一步， 3和5相乘， 结果15， 传给取反函数， 结果-15， 传给partial返回的函数， 该函数将-15乘以10返回最终结果-150.

这个例子模拟了通过在已经存在的函数上进行组合和部分应用， 我们可以创建出任意复杂的功能。 使用组合和部分应用， 也可以使你的代码更清晰简洁。 通常情况下， 可以通过对简短的函数进行组合和部分应用来替换那个复杂而庞大的函数。
## 总结(Putting It All Together) ##
这一章涵盖了Clojure程序中最基本的元素：函数，递归，条件分支逻辑。 为了更好地使用Clojure，得完全适应这些结构。

不像大部分其他的语言， Clojure可以在这些基本控制结构上进化出更复杂的结构， 我们可以直接的构造和使用这些更为复杂的结构。 当然， 仅仅使用基本结构， 我们也可以写出任意庞大任意复杂的程序。 条件、循环和函数调用是非常有用的， 毕竟， 有些语言只能使用这些。 但是， 这样的程序是横着长的， 只不过是越来越多的条件、函数、更复杂的逻辑或者递归的堆积而已。 修改或者扩展这样的程序的成本是线性的， 小修改或添加小功能只需要花一点功夫， 如果有大改动， 那就够你喝一壶了。

Clojure鼓励通过在已有的基本结构----而不是直接就使用它们----的基础上构建自己的控制结构， 从而让程序竖着长。使用作为一等对象的函数和闭包是让程序竖着长的极好方式。 从你自己的程序或者问题域中识别出特定的一些模式， 就有可能构建出你自己的控制结构， 这可比那些基本结构强悍得多。 你的程序也可以以次线性代价----做些小修改很简单， 即使是有大改动， 也不难， 因为这时语言已经是为你这问题哉定制过的----来扩展或者修改。

比方说， 完全有可能只通过手动使用递归来处理集合。 但集合操作对于提供了高阶集合操作----map,reduce,filter等等----的Clojure来讲是一项非常普通的任务。 这些都将在第五章中讨论， 这些高阶集合操作也可以将使集合操作表示成一系列单行调用而不是在每个场合都重新再来一次递归。 也可以把这种理念用到其他任何问题域上。 Clojure包含集合操作函数， 是因为几乎在每个问题域中都会使用到它们， 但你也可以在任何问题域上以特定的结构来处理相同的问题。 不要只是完成某个功能， 也要使用高阶函数（和稍后的宏）来构建可以辅助解决问题的（上层）工具。

当Clojure程序的复杂度达到一定程度， 如果程序经过良好的设计， 你会发现， 程序非常像一个经过高度定制化的领域特定语言（DSL）了。 这并不需要额外的工作， 只是就这么自然就发生， 而且----相比重复地使用基本结构----也确实可以让程序更小，更轻量一些。循环、递归和条件分支是很有用的， 但它们只应该是程序的构建模块， 而不是程序本身。当一个项目这样处理的时候， 你会很惊讶地发现程序只需要那么少的结构（因为结构都是定制给这个问题域的）。