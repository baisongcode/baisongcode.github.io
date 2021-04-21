---
layout: post
title:  用VS Code搭建Sketchup的ruby二次开发环境
date:   2021-4-20
categories: sketchup二次开发
---

## 简介

本文主要参考sketchup team在github上的入门教学项目[“sketchup-ruby-api-tutorials”中的wiki](https://github.com/SketchUp/sketchup-ruby-api-tutorials/wiki)，再加上网络上的其他文章，以及个人的实践操作，汇总而来。

相信本文读者对sketchup都有一定了解，所以SU相关的安装，插件等，不再详述。并假设您已将Sketchup 2020 专业版安装完毕。

对于ruby这种脚本语言来说，最简单直接的开发方式，就是在记事本里写代码，然后粘到ruby控制台里运行看结果。但如果开发稍微复杂些的项目，就力不从心了。本文介绍的这些开发环境配置，尤其是第二部分VS Code的配置，比如代码自动补全、静态检查等等，都是从软件工程的角度，为提高开发效率和质量而准备的。

除Windows和sketchup外，本开发环境使用的都是免费产品，直接给出官网下载链接，无须任何破解。版本信息如下：
- Win10 x64
- Sketchup 2020 专业版
- Ruby 2.5.5
- 微软 Visual Studio Code (VS Code) 1.55

<!--more-->

## 一、安装Ruby

#### 1.1 确定ruby版本
每个sketchup的发行版，都可能有不同的Ruby版本，比如SU 2020的ruby版本是2.5.5，SU 2021的ruby版本是2.7.1。为了保证代码的兼容性，最好安装和sketchup相同版本的ruby。查看ruby版本的方法，是在SU的ruby控制台中，输入
```ruby
puts RUBY_VERSION
```
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-10.png)

#### 1.2 下载安装

到[Ruby官网的下载页面](https://www.ruby-lang.org/zh_cn/downloads/)，因为是win平台，所以选RubyInstaller
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-20.png)

在RubyInstaller的页面中点download，因为2.5.5版本比较老了，所以点“Archives”，进入存档页面

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-30.png)

在左边第一列“Ruby+Devkit Installers”下，找到2.5.5（x64）版，下载

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-40.png)

双击下载到的rubyinstaller-devkit-2.5.5-1-x64.exe，注意在选择安装路径那一步时，把“Use UTF-8 as default external encoding”勾上，这样可以统一字符集，减少中文乱码的可能性。其他按照默认即可。

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-50.png)

在随后的MSYS2安装中，可以直接回车，不安装这项，我目前还没有发现问题。如果不放心的话，也可以选择1，安装完毕后再次回车退出即可。

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-60.png)

以上都安装完毕后，点开始菜单，输入“cmd”，进入windows的命令提示符环境，输入
```bat
ruby -v
```
能看到ruby的版本号，就说明ruby安装完毕。

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-70.png)

#### 1.3 修改Ruby Gem为国内源
RubyGems 是 Ruby 的一个包管理器，它提供一个分发 Ruby 程序和库的标准格式。你可以理解为ruby程序自己的app store。在Sketchup Ruby插件的开发过程中，有很多工具要靠gem来安装。但由于一些原因，国内访问gem总站rubygems.org很不稳定。

好在Ruby China社区做了gem库的国内镜像，所以我需要把默认的rubygems.org源修改到国内的gems.ruby-china.com源。

同样在上步的命令提示符环境下，查看当前的gem源，输入
```
gem sources -l
```
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-130.png)

运行以下命令，删除默认源：
```
gem sources --remove https://rubygems.org/
```
运行以下命令，增加国内源：
```
gem sources -a https://gems.ruby-china.com/
```
再次查看
```
gem sources -l
```
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-140.png)

可以看到已经修改为国内源了。

然后我们用国内源安装[bundler工具](https://www.bundler.cn/)，用它来管理ruby包，后面会用到。运行
```
gem install bundler
```
安装完成后，查看bundle版本号，来验证安装是否成功：
```
bundle -v
```
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-141.png)


## 二、安装VS Code并配置


去微软VS Code官网 [https://code.visualstudio.com/](https://code.visualstudio.com/) 下载，我当前下到的版本是1.55.2。然后默认安装即可。与RubyMine这种专门为Ruby开发而生的IDE不同，VS Code是一个通用的IDE，需要安装一些插件后，才能实现对ruby开发的支持：

1. 中文语言包。

点左边的Extensions选项卡，输入“Chinese”，找到中文语言包，点击“Install”

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-80.png)

重启后，界面就变成中文了。

2.在“扩展”选项卡下搜“ruby”，安装ruby插件。这个插件提供核心的ruby开发和调试功能。
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-90.png)

3.在上一项的搜索结果中，找到“Ruby Solargraph”插件并安装。这个插件提供了ruby代码的自动补全功能。
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-100.png)

4.再在上面的搜索结果中，找到“ruby-rubocop”插件并安装。这个插件提供了代码的静态检查功能。
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-110.png)

重启VScode，目前我们安装的插件如下图：

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-120.png)



## 三、初始化项目环境

#### 3.1 下载模板项目代码

Sketchup官方团队在github上有一个VScode下的空白项目模板：[sketchup-extension-vscode-project](https://github.com/SketchUp/sketchup-extension-vscode-project)，里面已经为sketchup开发预制好了：
- 代码自动完成配置（.solargraph.yml）
- Sketchup api调用检查（.rubocop.yml）
- VScode插件提醒
- 编辑器配置（.editorconfig，比如ruby社区约定用2个空格键代替tab等）

先下载项目代码：

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-180.png)

解压到D:\workspace\目录下，形成如下目录结构：

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-190.png)

打开VScode - 打开文件夹 - 选择“D:\workspace\sketchup-extension-vscode-project-master”

会提醒你一下推荐的其他插件，建议也装上。

这时，点开左边的main.rb，就有了点开发的模样了：

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-200.png)

但是！因为这个项目是2019年的，所以里面有些配置有些过时，需要更新一下：（说实话我写到这里也有点烦了，真不省心呀，但坚持一下哈，快看到曙光了！）

需要修改的文件如下图：

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-210.png)

#### 3.2 修改Gemfile文件
- 把第一行的默认源修改为国内源
- 把rubocop的版本号从0.63.1更改为0.93.0。rubocop最新版本已经到了1.9.x了，但新版本和sketchup的规则冲突，所以这里指定一个旧版本
- 把rubocop-sketchup的版本从0.8.0更新为0.18.0，这是截至2021年4月的最新版本。后期自己可以到[rubocop-sketchup的github页](https://github.com/SketchUp/rubocop-sketchup)检查是否更新。

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-220.png)

这是修改后的Gemfile，可以直接覆盖源内容
```yml
#source 'https://rubygems.org' 
source 'https://gems.ruby-china.com' # 更新为国内源

group :development do
  gem 'minitest' # Helps solargraph with code insight when you write unit tests.
  # gem 'rubocop', '~> 0.63.1' # Static analysis of Ruby Code.
  gem 'rubocop', '~> 0.93.0' # 最新版本和rubocop-sketchup冲突，指定0.93版本
  # gem 'rubocop-sketchup', '~> 0.8.0' # Static analysis of SketchUp extensions.
  gem 'rubocop-sketchup', '~> 0.18.0'  # 更新版本
  gem 'sketchup-api-stubs' # Auto-complete for the SketchUp Rub API.
  gem 'skippy', '~> 0.4.1.a' # Aid with common SketchUp extension tasks.
  gem 'solargraph' # For code-insight and RuboCop integration with VSCode.
end
```
#### 3.3 修改.rubocop.yml文件
- 把TargetSketchUpVersion从2014修改为你自己的版本，这里我修改为2020
- 把TargetSketchUpVersion从2.2修改为你自己的版本，这里我改为2.5

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-230.png)

另外，多说一句，第12行的“DisabledByDefault: true”是rubocop一般静态检查规则的开关，如果设为false的话，就会把[ruby社区的代码规范](https://rubystyle.guide/)都检查一遍，对代码质量有追求的开发者建议打开此检查。但其中有些规则是不适合中文的，比如出现中文注释就会提醒等，我把自己的.rubocop.yml贴上，供参考：

```yml
require: rubocop-sketchup

# If you want to use the same codding pattern as SketchUp's projects, enable
# the next line. RuboCop will then use the coding pattern from the
# rubocop-sketchup project. This coding pattern is a more relaxed version than
# the default RuboCop pattern.
# inherit_from: https://raw.githubusercontent.com/SketchUp/rubocop-sketchup/master/sketchup-style.yml

AllCops:
  # This prevents normal RuboCop cops to run. Disable this to get full static
  # analysis of your Ruby code.
  DisabledByDefault: true

  DisplayCopNames: true
  DisplayStyleGuide: true
  ExtraDetails: true
  Exclude:
  - src/*/vendor/**/* # Exclude skippy vendor folder
  - src/ruby/main.rb 
  SketchUp:
    SourcePath: src
    # TargetSketchUpVersion: 2014
    TargetSketchUpVersion: 2020
    Exclude: # Exclude common folders.
    - profiling/
    - skippy/
    - tests/
   #TargetRubyVersion: 2.2 # Should have been 2.0 but RuboCop dropped support.
   TargetRubyVersion: 2.5 

# 根据自己项目修改的一些规则
Style/AsciiComments:
  Enabled: false

SketchupRequirements/FileStructure:
  Enabled: false

# If DisabledByDefault is set to true then we need to enable the SketchUp
# related departments:

SketchupDeprecations:
  Enabled: true

SketchupPerformance:
  Enabled: true

SketchupRequirements:
  Enabled: true

SketchupSuggestions:
  Enabled: true

SketchupBugs:
  Enabled: true

```

#### 3.4 修改.solargraph.yml文件
- 把第2行的SU安装路径改为你自己的

```yml
require_paths:
# - "C:/Program Files/SketchUp/SketchUp 2019/Tools"
- "C:/Program Files/SketchUp/SketchUp 2020/Tools" #改为自己的安装路径
- src

require:
- sketchup-api-stubs

reporters:
- rubocop
```
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-240.png)

#### 3.5 用bundle安装Gemfile中的依赖
3.2步骤中修改好的Gemfile文件中，列的都是静态检查，自动补全需要的工具包，现在需要把他们都安装到本机。

先在windows命令行下换到D盘
```
d:
```
进入到项目根目录，也就是Gemfile文件所在的文件夹
```
cd D:\workspace\sketchup-extension-vscode-project-master
```
然后运行
```
bundle install
```

就开始安装一大批依赖包。完毕后，再打开VS Code，稍等十几秒，代码自动完成，静态检查等功能就出现了！

比如，我输入“Sketchup::Set”，rubocop就会提示你这是个废弃的class。

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-250.png)

## 四、自动reload开发目录下的插件代码

现在你就可以开始全副武装的SU插件开发了，但慢慢又会发现一个问题：每次都要把代码拷贝到SU的plugin目录下，再重启SU才能看到更改结果，太麻烦了！

解决方法就是参考[sketchup-ruby-api-tutorials里的wiki](https://github.com/SketchUp/sketchup-ruby-api-tutorials/wiki/Development-Setup)，步骤如下：

#### 4.1 把工作目录下的代码载入到SU的plugin目录下

在SU的plugin目录下（我电脑上是C:\Users\baisong\AppData\Roaming\SketchUp\SketchUp 2020\SketchUp\Plugins），新建一个!vscode-project-master.rb文件，当然文件名也可以改成别的，代码如下:
```ruby
paths = [
  "D:/workspace/sketchup-extension-vscode-project-master/src", #你自己代码的路径
  # Add more as needed here...
]

SKETCHUP_CONSOLE.show  # 显示ruby控制台

paths.each { |path|
  $LOAD_PATH << path
  Dir.glob("#{path}/*.rb") { |file|
    Sketchup.require(file)
  }
}
```

这样就可以把你在D盘的插件代码，载入到C盘的SU下了

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-260.png)

#### 4.2 动态载入代码，避免重启SU

还是以3.1的模板项目为例，在最顶层的ruby文件ex_hello_cube.rb中的module Examples层下，加入如下代码：

```ruby
  def self.reload
    original_verbose = $VERBOSE
    $VERBOSE = nil
    pattern = File.join(__dir__, '**/*.rb')
    Dir.glob(pattern).each { |file|
      # Cannot use `Sketchup.load` because its an alias for `Sketchup.require`.
      load file
    }.size
  ensure
    $VERBOSE = original_verbose
  end
```
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-270.png)

重启一下SU，再进入ruby控制台，运行
```ruby
Examples::reload
```
就可以重新载入代码了。

为了验证，我们在main.rb中的Examples::HelloCube.create_cube方法中，加入
```ruby
puts "http://baisong.me"
```
![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-280.png)

进入ruby控制台，再运行
```ruby
Examples::reload
```
再点击插件的菜单，就会发现这次在创建立方体的同时，也运行了后台新增的打印语句。

![image](/img/2021/2021-4-20-vscode-sketchup-dev-setup/2021-4-20-vscode-sketchup-dev-setup-290.png)

## 五、参考资料

- [Ruby菜鸟教程](https://www.runoob.com/ruby/ruby-tutorial.html)
- [sketchup-ruby-api-tutorials](https://github.com/SketchUp/sketchup-ruby-api-tutorials)
- [Sketchup Community](https://forums.sketchup.com/categories)

