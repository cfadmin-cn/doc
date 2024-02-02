## 一、概念

  `class`(中文: 类)在编程语言中是面向对象编程(`OOP`)的构造方式，它用来定义了的对象的所有属性和行为.

  开发者能合理的封装来整合零碎的函数, 并通过记录内部状态隐藏了方法的具体实现, 而对外可以仅输出简单的接口.

  `Lua`并不是面向对象的编程语言, 但是我们可以通过元方法实现这项功能。让所有开发者使用面向对象的方式编写代码.

## 二、名词

  由于不同语言对`父类`在一般情况下代指`基类`、与之对应的就是`子类`在这种情况就会代指`派生类`.

### 1. `class`的特性:

  * `abstract` - 用**人类可读性较好**的方式来描述对一个事、物的认知, 类的抽象层次越高可读性就更高;

  * `inheritance` - 继承能很好的用来提升**代码复用性**, 如果可以配合其它特性就能实现更多高级设计模式;

  * `encapsulation` - 将原本的函数进行**模块化**后通过封装的方法**统一调用**, 类封装的越好使用就越简单;

  * `overwite/override` - 写的不好？不够强大? 合理的使用此方法, 对代码本身都是一种优化;

### 2. `class`的优势:

    * 抽象性好, 业务适应性较好;

    * 复用性高, 降低代码冗余量;

    * 代码直观, 快速提高可读性;

    * 无需依赖, 源码实现更简单;

## 三、原型

  以下就是`class`函数的原型:

  ```lua
  -- lualib/class/init.lua
  function class(cname, cmeta) ... return cls end
  ```

  `cname` - `string`类型的类名, 它通常用于调试.

  `cmeta` - 必须是`class`的返回值或省略, 通常用于父、子类的继承操作.

  创建成功会返回一个类; 这个类可以用来进行后续定义(因为在`Lua`里, 定义就是声明).

## 四、使用

  通过`local class = require "class"`导入后, 然后我们就可以开始使用了.

  使用之前我们先来学习基础:

  1. **类实例**的创建与初始化方法分为`2`种: `cls()` 与 `clas:new()`;

  2. 基类**必须**定义`ctor`的构造函数, 而派生类可以**共用基类构造函数**所以可选;

  3. 类方法最好使用`function cls:method() end` 的规范来显示并直观的定义;

  在了解完成基础后, 我们开始学习`class`的编写方式.

### 0. 定义基类

  ```lua
  -- Animal.lua
  -- 动物类 - 基类
  local class = require "class"

  local Animal = class("Animal")

  function Animal:ctor(opt)
    self.distance = opt.distance or 0
    self:init(opt)
  end

  function Animal:init(opt)
    -- TODO
  end

  function Animal:Move(distance)
    self.distance = distance + 1
  end

  return Animal
  ```

  我们定义一个`Animal`类, 然后我们为它定义构造函数`ctor`(必须)并做一些初始化, 然后定义并且声明`init`一个空的方法, 这个空方法留在之后我们再使用.

  最后编写了一个方法`Move`, 这个`Move`方法可以表示为我们**动物每次移动的基础距离**.

### 1. 抽象派生

  ```lua
  -- Tortoise.lua
  -- 乌龟类 - 派生类
  local Animal = require "Animal"

  local class = require "class"

  local Tortoise = class("Tortoise", Animal)

  return Tortoise
  ```

  ```lua
  -- Rabbit.lua
  -- 兔子类 - 派生类
  local Animal = require "Animal"

  local class = require "class"

  local Rabbit = class("Rabbit", Animal)

  return Rabbit
  ```

  `Tortoise`和`Rabbit`都是动物属的, 所以我们可以选择直接从`Animal`继承.

  在这里细心的小伙伴发现了, 我们并没有编写构造函数就直接可以使用了! 因为`Animal`的派生类可以直接得到`Animal`的构造函数与所有方法.

  现在我们得到了两个很使用的**抽象类**, 并且它们分别可以表示两种动物属科的大分类.

### 2. 覆盖重写

  ```lua
  -- Tortoise.lua
  -- 乌龟类 - 派生类
  local Animal = require "Animal"

  local class = require "class"

  local Tortoise = class("Tortoise", Animal)

  function Tortoise:Move()
    -- 乌龟移动缓慢
    self.distance = self.distance + 0.5
  end
  ```

  ```lua
  -- Rabbit.lua
  -- 兔子类 - 派生类
  local Animal = require "Animal"

  local class = require "class"

  local Rabbit = class("Rabbit", Animal)

  function Rabbit:Move()
    -- 兔子移动迅速.
    self.distance = self.distance + 5
  end
  ```

  在使用`Tortoise`和`Rabbit`类一段时间后, 我们发现很多地方都用到了`Move`方法. 
  
  现在的你发现了不对! `Tortoise`和`Rabbit`之间的移动距离是不同的, 所以我们必须覆盖后重写派生类方法.

  重写的我们必须将`Tortoise`和`Rabbit`的数值需改正确, 并且增加上很好的注释方便修改.

### 3. 自行扩展

  ```lua
  -- Eagle.lua
  -- 鹰类 - 派生类
  local Animal = require "Animal"

  local class = require "class"

  local Eagle = class("Eagle", Animal)

  function Eagle:Move()
    -- 鹰移动迅速, 所以有距离.
    self.distance = self.distance + 1
  end

  function Eagle:Fly()
    -- 鹰可以飞起来，所以还有高度.
    self.highly = self.highly + 5
    -- 鹰飞行的移动距离计算
    self.distance = self.distance * 2
  end

  return Eagle
  ```

  没错, 我们还要补齐还缺少的老鹰! 所以派生类的必须加上`高度(highly)`的概念, 并且增加`Fly`方法来调整.

  但是这还有点问题! 如果直接运行`Fly`方法就会报错, 那么我们必须再做点什么.

### 4. 二次构造

  ```lua
  -- Eagle.lua
  -- 鹰类 - 派生类
  local Animal = require "Animal"

  local class = require "class"

  local Eagle = class("Eagle", Animal)

  function Eagle:init(opt)
    self.highly = opt.highly or 0
  end

  function Eagle:Move()
    -- 鹰移动迅速, 所以有距离.
    self.distance = self.distance + 1
  end

  function Eagle:Fly()
    -- 鹰可以飞起来，所以还有高度.
    self.highly = self.highly + 5
    -- 鹰飞行的移动距离计算
    self.distance = self.distance * 2
  end

  return Eagle
  ```

  是的! 由于我们重写了`init`方法, 现在运行`Fly`不会在出错了. 在派生类中需要先初始化基类时可以尝试这么做.

  但是有时候我们需要重写构造方法, 而新的构造方法内部必须做一些特殊的操作.

### 4. 重新构造

  ```lua
  -- Eagle.lua
  -- 鹰类 - 派生类
  local Animal = require "Animal"

  local class = require "class"

  local Eagle = class("Eagle", Animal)

  function Eagle:ctor(opt)
    -- 假设下面的代码块做了一点操作.
    -- TODO
    -- 假设上面的代码块做了一点操作.
    self.distance = opt.distance or 0
    self.highly = opt.highly or 0
    self:init(opt)
  end

  function Eagle:Move()
    -- 鹰移动迅速, 所以有距离.
    self.distance = self.distance + 1
  end

  function Eagle:Fly()
    -- 鹰可以飞起来，所以还有高度.
    self.highly = self.highly + 5
    -- 鹰飞行的移动距离计算
    self.distance = self.distance * 2
  end

  return Eagle
  ```

  可以看到, 无论哪种方式最后都可以完成我们的目的. 并且我们还是保留了`init`方法, 为**后续派生**的提供了扩展.

## 五、测试

```lua
-- script/demo.lua
local LOG = require "logging"
local class = require "class"

-- 类 A
local A = class("A")

function A:ctor()
	self.tname = "A"
end

function A:Echo()
	return self.tname
end

-- 类 B
local B = class("B", A)

-- 类 C
local C = class("C", B)

-- start debug

LOG:DEBUG(A)
LOG:DEBUG(A())
LOG:DEBUG(A:new():Echo())

LOG:DEBUG(B)
LOG:DEBUG(B())
LOG:DEBUG(B:new():Echo())

LOG:DEBUG(C)
LOG:DEBUG(C())
LOG:DEBUG(C:new():Echo())
```

```bash
[candy@MacBookPro:~/Documents/cfadmin] $ ./cfadmin -e script/demo.lua
[2021-08-22 17:38:39,300] [@script/demo.lua:23] [DEBUG] : A{["__name"]="A", ["ctor"]=function: 0x7fda22d0ea10, ["Echo"]=function: 0x7fda22d0d1e0}
[2021-08-22 17:38:39,300] [@script/demo.lua:24] [DEBUG] : A{["tname"]="A"}
[2021-08-22 17:38:39,300] [@script/demo.lua:25] [DEBUG] : "A"
[2021-08-22 17:38:39,300] [@script/demo.lua:27] [DEBUG] : B{["__name"]="B", ["ctor"]=function: 0x7fda22d0ea10, ["Echo"]=function: 0x7fda22d0d1e0}
[2021-08-22 17:38:39,300] [@script/demo.lua:28] [DEBUG] : B{["tname"]="A"}
[2021-08-22 17:38:39,300] [@script/demo.lua:29] [DEBUG] : "A"
[2021-08-22 17:38:39,300] [@script/demo.lua:31] [DEBUG] : C{["__name"]="C", ["ctor"]=function: 0x7fda22d0ea10, ["Echo"]=function: 0x7fda22d0d1e0}
[2021-08-22 17:38:39,300] [@script/demo.lua:32] [DEBUG] : C{["tname"]="A"}
[2021-08-22 17:38:39,300] [@script/demo.lua:33] [DEBUG] : "A"
^C
[candy@MacBookPro:~/Documents/cfadmin] $
```

## 六、底层

  `Lua`实现的`class`不同于其它编程语言的原生类. 它由框架内置的一些`Lua`代码实现，通过**元方法**模拟类的行为来抽象出本章节我们提出的基本概念.

  我们的所有**类**在最初都是由`Anonymous`衍生和创建而来, `Lua`再通过它重复**继承**和**派生**形成独有的**派生链**. **派生链**从设计意义上来说就是类似于`python`的`mro`和`javascript`的`__proto__`, 但是并不会在代码中实际存在.
  
  **继承链**对于`Lua`的`metatable`体系来说还是太过复杂了! 所以我们选择最简单的方法来实现: **函数方法**都是是引用类型, 所以我们可以直接从**基类**拷贝到**派生类**. 因为**这样的代码看起来是绝对没有问题**的.