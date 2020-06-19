---
title: 😝 - 用插件扩展 kubectl
reviewers:
- juanvallejo
- soltysh
description: 使用 kubectl 插件，你可以通过添加新的子命令来扩展 kubectl 命令的功能。
---

<!--
title: Extend kubectl with plugins
reviewers:
- juanvallejo
- soltysh
description: With kubectl plugins, you can extend the functionality of the kubectl command by adding new subcommands.
content_type: task
-->

<!-- overview -->

. feature-state state="stable" >}}

<!--
This guide demonstrates how to install and write extensions for [kubectl](/docs/reference/kubectl/kubectl/). By thinking of core `kubectl` commands as essential building blocks for interacting with a Kubernetes cluster, a cluster administrator can think
of plugins as a means of utilizing these building blocks to create more complex behavior. Plugins extend `kubectl` with new sub-commands, allowing for new and custom features not included in the main distribution of `kubectl`.
-->
本指南演示了如何为 [kubectl](/docs/reference/kubectl/kubectl/) 安装和编写扩展。
通过将核心 `kubectl` 命令看作与 Kubernetes 集群交互的基本构建块，集群管理员可以将插件视为一种利用这些构建块创建更复杂行为的方法。
插件用新的子命令扩展了 `kubectl`，允许新的和自定义的特性不包括在 `kubectl` 的主要发行版中。



## . heading "prerequisites" %}}


<!--
You need to have a working `kubectl` binary installed.
-->
您需要安装一个工作的二进制 `kubectl`。

. note >}}

<!--
Plugins were officially introduced as an alpha feature in the v1.8.0 release. They have been re-worked in the v1.12.0 release to support a wider range of use-cases. So, while some parts of the plugins feature were already available in previous versions, a `kubectl` version of 1.12.0 or later is recommended if you are following these docs.
-->
插件在 v1.8.0 版本中正式作为 alpha 特性引入。它们已经在 v1.12.0 版本中工作，以支持更广泛的用例。因此，虽然在以前的版本中已经提供了部分插件特性，但如果您遵循这些文档，建议使用 1.12.0 或更高版本的 `kubectl`。

. /note >}}



<!-- steps -->

<!--
## Installing kubectl plugins
-->

## 安装 kubectl 插件

<!--
A plugin is nothing more than a standalone executable file, whose name begins with `kubectl-`. To install a plugin, simply move this executable file to anywhere on your PATH.
-->
插件只不过是一个独立的可执行文件，名称以 `kubectl-` 开头。要安装插件，只需将此可执行文件移动到路径上的任何位置。

. note >}}

<!--
Kubernetes does not provide a package manager or anything similar to install or update plugins. It is your responsibility to ensure that plugin executables have a filename that begins with `kubectl-`, and that they are placed somewhere on your PATH.
-->
Kubernetes 不提供包管理器或任何类似于安装或更新插件的东西。你有责任确保插件可执行文件的文件名以 `kubectl-` 开头，并将它们放在你路径的某个位置。
. /note >}}

<!--
### Discovering plugins
-->

### 发现插件

<!--
`kubectl` provides a command `kubectl plugin list` that searches your PATH for valid plugin executables.
Executing this command causes a traversal of all files in your PATH. Any files that are executable, and begin with `kubectl-` will show up *in the order in which they are present in your PATH* in this command's output.
A warning will be included for any files beginning with `kubectl-` that are *not* executable.
A warning will also be included for any valid plugin files that overlap each other's name.
-->
`kubectl` 提供一个命令 `kubectl plugin list`，用于搜索路径查找有效的插件可执行文件。
执行此命令将遍历路径中的所有文件。任何以 `kubectl-` 开头的可执行文件都将在这个命令的输出中以它们在路径中出现的顺序显示。
任何以 `kubectl-` 开头的文件如果`不可执行`，都将包含一个警告。
对于任何相同的有效插件文件，都将包含一个警告。

<!--
#### Limitations
-->

#### 限制

<!--
It is currently not possible to create plugins that overwrite existing `kubectl` commands. For example, creating a plugin `kubectl-version` will cause that plugin to never be executed, as the existing `kubectl version` command will always take precedence over it. Due to this limitation, it is also *not* possible to use plugins to add new subcommands to existing `kubectl` commands. For example, adding a subcommand `kubectl create foo` by naming your plugin `kubectl-create-foo` will cause that plugin to be ignored. Warnings will appear under the output of `kubectl plugin list` for any valid plugins that attempt to do this.
-->
目前无法创建覆盖现有 `kubectl` 命令的插件，例如，创建一个插件 `kubectl-version` 将导致该插件永远不会被执行，因为现有的 `kubectl-version` 命令总是优先于它执行。
由于这个限制，也不可能使用插件将新的子命令添加到现有的 `kubectl` 命令中。例如，通过将插件命名为 `kubectl-create-foo` 来添加子命令 `kubectl create foo` 将导致该插件被忽略。对于任何试图这样做的有效插件 `kubectl plugin list` 的输出中将显示警告。

<!--
## Writing kubectl plugins
-->

## 编写 kubectl 插件

<!--
You can write a plugin in any programming language or script that allows you to write command-line commands.
-->
你可以用任何编程语言或脚本编写插件，允许您编写命令行命令。

<!--
There is no plugin installation or pre-loading required. Plugin executables receive the inherited environment from the `kubectl` binary.
A plugin determines which command path it wishes to implement based on its name. For example, a plugin wanting to provide a new command
`kubectl foo`, would simply be named `kubectl-foo`, and live somewhere in the user's PATH.
-->
不需要安装插件或预加载，插件可执行程序从 `kubectl` 二进制文件接收继承的环境，插件根据其名称确定它希望实现的命令路径。
例如，一个插件想要提供一个新的命令 `kubectl foo`，它将被简单地命名为 `kubectl-foo`，并且位于用户路径的某个位置。

<!--
### Example plugin
-->

### 示例插件

```
#!/bin/bash

# optional argument handling
if [[ "$1" == "version" ]]
then
    echo "1.0.0"
    exit 0
fi

# optional argument handling
if [[ "$1" == "config" ]]
then
    echo $KUBECONFIG
    exit 0
fi

echo "I am a plugin named kubectl-foo"
```

<!--
### Using a plugin
-->

### 使用插件

<!--
To use the above plugin, simply make it executable:
-->
要使用上面的插件，只需使其可执行：

```
sudo chmod +x ./kubectl-foo
```

<!--
and place it anywhere in your PATH:
-->
并将它放在你的路径中的任何地方：

```
sudo mv ./kubectl-foo /usr/local/bin
```

<!--
You may now invoke your plugin as a `kubectl` command:
-->
你现在可以调用你的插件作为 `kubectl` 命令：

```
kubectl foo
```
```
I am a plugin named kubectl-foo
```

<!--
All args and flags are passed as-is to the executable:
-->
所有参数和标记按原样传递给可执行文件：

```
kubectl foo version
```
```
1.0.0
```

<!--
All environment variables are also passed as-is to the executable:
-->
所有环境变量也按原样传递给可执行文件：

```bash
export KUBECONFIG=~/.kube/config
kubectl foo config
```
```
/home/<user>/.kube/config
```

```shell
KUBECONFIG=/etc/kube/config kubectl foo config
```
```
/etc/kube/config
```

<!--
Additionally, the first argument that is passed to a plugin will always be the full path to the location where it was invoked (`$0` would equal `/usr/local/bin/kubectl-foo` in our example above).
-->
此外，传递给插件的第一个参数总是调用它的位置的绝对路径（在上面的例子中，`$0` 将等于 `/usr/local/bin/kubectl-foo`）。

<!--
### Naming a plugin
-->

### 命名插件

<!--
As seen in the example above, a plugin determines the command path that it will implement based on its filename. Every sub-command in the command path that a plugin targets, is separated by a dash (`-`).
For example, a plugin that wishes to be invoked whenever the command `kubectl foo bar baz` is invoked by the user, would have the filename of `kubectl-foo-bar-baz`.
-->
如上面的例子所示，插件根据文件名确定要实现的命令路径，插件所针对的命令路径中的每个子命令都由破折号（`-`）分隔。
例如，当用户调用命令 `kubectl foo bar baz` 时，希望调用该命令的插件的文件名为 `kubectl-foo-bar-baz`。

<!--
#### Flags and argument handling
-->

#### 参数和标记处理

. note >}}
<!--
Unlike previous versions of `kubectl`, the plugin mechanism will _not_ create any custom, plugin-specific values or environment variables to a plugin process.
This means that environment variables such as `KUBECTL_PLUGINS_CURRENT_NAMESPACE` are no longer provided to a plugin. Plugins must parse all of the arguments passed to them by a user,
and handle flag validation as part of their own implementation. For plugins written in Go, a set of utilities has been provided under [k8s.io/cli-runtime](https://github.com/kubernetes/cli-runtime) to assist with this.
-->
与以前版本的 `kubectl` 不同，插件机制不会为插件进程创建任何定制的、特定于插件的值或环境变量，这意味着像 `KUBECTL_PLUGINS_CURRENT_NAMESPACE` 这样的环境变量不再提供给插件。
插件必须解析用户传递给它们的所有参数，并将参数验证作为它们自己实现的一部分处理。对于用 Go 编写的插件，在 [k8s.io/cli-runtime](https://github.com/kubernetes/cli-runtime) 下提供了一组实用程序来帮助实现这一点。

. /note >}}

<!--
Taking our `kubectl-foo-bar-baz` plugin from the above scenario, we further explore additional cases where users invoke our plugin while providing additional flags and arguments.
For example, in a situation where a user invokes the command `kubectl foo bar baz arg1 --flag=value arg2`, the plugin mechanism will first try to find the plugin with the longest possible name, which in this case
would be `kubectl-foo-bar-baz-arg1`. Upon not finding that plugin, it then treats the last dash-separated value as an argument (`arg1` in this case), and attempts to find the next longest possible name, `kubectl-foo-bar-baz`.
Upon finding a plugin with this name, it then invokes that plugin, passing all args and flags after its name to the plugin executable.
-->
从上面的场景中使用我们的 `kubectl-foo-bar-baz` 插件，我们将进一步研究用户在提供额外标记和参数的同时调用我们的插件的其他情况。
例如，在用户调用命令 `kubectl foo bar baz arg1 --flag=value arg2` 的情况下，插件机制将首先尝试找到名称可能最长的插件，在本例中是 `kubectl-foo-bar-baz-arg1`。
当没有找到这个插件时，它就会将最后一个以破折号分隔的值视为参数（在本例中为 `arg1`），并尝试找到下一个最长的名称 `kubectl-foo-bar-baz`。
在找到具有此名称的插件后，它将调用该插件，并在其名称之后将所有参数和标志传递给插件可执行文件。

<!--
Example:
-->
示例：

```bash
# create a plugin
echo -e '#!/bin/bash\n\necho "My first command-line argument was $1"' > kubectl-foo-bar-baz
sudo chmod +x ./kubectl-foo-bar-baz

# "install" our plugin by placing it on our PATH
sudo mv ./kubectl-foo-bar-baz /usr/local/bin

# ensure our plugin is recognized by kubectl
kubectl plugin list
```
```
The following kubectl-compatible plugins are available:

/usr/local/bin/kubectl-foo-bar-baz
```
```
# test that calling our plugin via a "kubectl" command works
# even when additional arguments and flags are passed to our
# plugin executable by the user.
kubectl foo bar baz arg1 --meaningless-flag=true
```
```
My first command-line argument was arg1
```

<!--
As you can see, our plugin was found based on the `kubectl` command specified by a user, and all extra arguments and flags were passed as-is to the plugin executable once it was found.
-->
正如你所看到的，我们的插件是基于用户指定的 `kubectl` 命令找到的，所有额外的参数和标记都是按原样传递给插件可执行文件的。

<!--
#### Names with dashes and underscores
-->

#### 带有破折号和下划线的名称

<!--
Although the `kubectl` plugin mechanism uses the dash (`-`) in plugin filenames to separate the sequence of sub-commands processed by the plugin, it is still possible to create a plugin
command containing dashes in its commandline invocation by using underscores (`_`) in its filename.
-->
虽然 `kubectl` 插件机制在插件文件名中使用破折号（`-`）分隔插件处理的子命令序列，但是仍然可以通过在文件名中使用下划线（`-`）来创建命令行中包含破折号的插件命令。

<!--
Example:
-->

例子：

```bash
# create a plugin containing an underscore in its filename
echo -e '#!/bin/bash\n\necho "I am a plugin with a dash in my name"' > ./kubectl-foo_bar
sudo chmod +x ./kubectl-foo_bar

# move the plugin into your PATH
sudo mv ./kubectl-foo_bar /usr/local/bin

# our plugin can now be invoked from `kubectl` like so:
kubectl foo-bar
```
```
I am a plugin with a dash in my name
```

<!--
Note that the introduction of underscores to a plugin filename does not prevent us from having commands such as `kubectl foo_bar`.
The command from the above example, can be invoked using either a dash (`-`) or an underscore (`_`):
-->
请注意，在插件文件名中引入下划线并不会阻止我们使用 `kubectl foo_bar` 之类的命令。可以使用破折号（`-`）或下划线（`-`）调用上面示例中的命令:

```bash
# our plugin can be invoked with a dash
kubectl foo-bar
```
```
I am a plugin with a dash in my name
```

```bash
# it can also be invoked using an underscore
kubectl foo_bar
```
```
I am a plugin with a dash in my name
```

<!--
#### Name conflicts and overshadowing
-->

#### 命名冲突和弊端

<!--
It is possible to have multiple plugins with the same filename in different locations throughout your PATH.
For example, given a PATH with the following value: `PATH=/usr/local/bin/plugins:/usr/local/bin/moreplugins`, a copy of plugin `kubectl-foo` could exist in `/usr/local/bin/plugins` and `/usr/local/bin/moreplugins`,
such that the output of the `kubectl plugin list` command is:
-->
可以在路径的不同位置使用多个文件名相同的插件，
例如，给定一个路径的值为: `PATH=/usr/local/bin/plugins:/usr/local/bin/moreplugins`，在 `/usr/local/bin/plugins` 和 `/usr/local/bin/moreplugins` 中可以存在一个插件 `kubectl-foo` 的副本，这样 `kubectl plugin list` 命令的输出就是:

```bash
PATH=/usr/local/bin/plugins:/usr/local/bin/moreplugins kubectl plugin list
```
```bash
The following kubectl-compatible plugins are available:

/usr/local/bin/plugins/kubectl-foo
/usr/local/bin/moreplugins/kubectl-foo
  - warning: /usr/local/bin/moreplugins/kubectl-foo is overshadowed by a similarly named plugin: /usr/local/bin/plugins/kubectl-foo

error: one plugin warning was found
```

<!--
In the above scenario, the warning under `/usr/local/bin/moreplugins/kubectl-foo` tells us that this plugin will never be executed. Instead, the executable that appears first in our PATH, `/usr/local/bin/plugins/kubectl-foo`, will always be found and executed first by the `kubectl` plugin mechanism.
-->
在上面的场景中 `/usr/local/bin/moreplugins/kubectl-foo` 下的警告告诉我们这个插件永远不会被执行。相反，首先出现在我们路径中的可执行文件 `/usr/local/bin/plugins/kubectl-foo` 总是首先被 `kubectl` 插件机制找到并执行。

<!--
A way to resolve this issue is to ensure that the location of the plugin that you wish to use with `kubectl` always comes first in your PATH. For example, if we wanted to always use `/usr/local/bin/moreplugins/kubectl-foo` anytime that the `kubectl` command `kubectl foo` was invoked, we would simply change the value of our PATH to be `PATH=/usr/local/bin/moreplugins:/usr/local/bin/plugins`.
-->
解决这个问题的一种方法是你确保你希望与 `kubectl` 一起使用的插件的位置总是在你的路径中首先出现。
例如，如果我们总是想使用 `/usr/local/bin/moreplugins/kubectl foo`，那么在调用 `kubectl` 命令 `kubectl foo` 时，我们只需将路径的值更改为 `PATH=/usr/local/bin/moreplugins:/usr/local/bin/plugins`。


<!--
#### Invocation of the longest executable filename
-->

#### 调用最长的可执行文件名

<!--
There is another kind of overshadowing that can occur with plugin filenames. Given two plugins present in a user's PATH `kubectl-foo-bar` and `kubectl-foo-bar-baz`, the `kubectl` plugin mechanism will always choose the longest possible plugin name for a given user command. Some examples below, clarify this further:
-->
对于插件文件名而言还有另一种弊端，给定用户路径中的两个插件 `kubectl-foo-bar` 和 `kubectl-foo-bar-baz` ，`kubectl` 插件机制总是为给定的用户命令选择尽可能长的插件名称。下面的一些例子进一步的说明了这一点：

```bash
# for a given kubectl command, the plugin with the longest possible filename will always be preferred
kubectl foo bar baz
```
```
Plugin kubectl-foo-bar-baz is executed
```

```bash
kubectl foo bar
```
```
Plugin kubectl-foo-bar is executed
```

```bash
kubectl foo bar baz buz
```
```
Plugin kubectl-foo-bar-baz is executed, with "buz" as its first argument
```

```bash
kubectl foo bar buz
```
```
Plugin kubectl-foo-bar is executed, with "buz" as its first argument
```

<!--
This design choice ensures that plugin sub-commands can be implemented across multiple files, if needed, and that these sub-commands can be nested under a "parent" plugin command:
-->
这种设计选择确保插件子命令可以跨多个文件实现，如果需要，这些子命令可以嵌套在"父"插件命令下：

```bash
ls ./plugin_command_tree
```
```
kubectl-parent
kubectl-parent-subcommand
kubectl-parent-subcommand-subsubcommand
```

<!--
### Checking for plugin warnings
-->

### 检查插件警告

<!--
You can use the aforementioned `kubectl plugin list` command to ensure that your plugin is visible by `kubectl`, and verify that there are no warnings preventing it from being called as a `kubectl` command.
-->
你可以使用前面提到的 `kubectl plugin list` 命令来确保你的插件可以被 `kubectl` 看到，并且验证没有警告防止它被称为 `kubectl` 命令。

```bash
kubectl plugin list
```
```
The following kubectl-compatible plugins are available:

test/fixtures/pkg/kubectl/plugins/kubectl-foo
/usr/local/bin/kubectl-foo
  - warning: /usr/local/bin/kubectl-foo is overshadowed by a similarly named plugin: test/fixtures/pkg/kubectl/plugins/kubectl-foo
plugins/kubectl-invalid
  - warning: plugins/kubectl-invalid identified as a kubectl plugin, but it is not executable

error: 2 plugin warnings were found
```

<!--
### Using the command line runtime package
-->

### 使用命令行 runtime 包

<!--
As part of the plugin mechanism update in the v1.12.0 release, an additional set of utilities have been made available to plugin authors. These utilities
exist under the [k8s.io/cli-runtime](https://github.com/kubernetes/cli-runtime) repository, and can be used by plugins written in Go to parse and update
a user's KUBECONFIG file, obtain REST clients to talk to the API server, and automatically bind flags associated with configuration and printing.
-->
作为 v1.12.0 版本中插件机制更新的一部分，插件作者可以使用另外一组实用程序。这些实用程序在，
[k8s.io/cli-runtime](https://github.com/kubernetes/cli-runtime) 存储库，并且可以被编写在 Go 中的插件用来解析和更新，
用户的 KUBECONFIG 文件，获取 REST 客户端与 API 服务器通信，并自动绑定与配置和打印相关的参数。

<!--
Plugins *do not* have to be written in Go in order to be recognized as valid plugins by `kubectl`, but they do have to use Go in order to take advantage of
the tools and utilities in the CLI Runtime repository.
-->
插件*不*必须用 Go 编写才能被 `kubectl` 识别为有效的插件，但是它们必须使用 Go 才能使用的 CLI Runtime 存储库中的工具和实用程序。

<!--
See the [Sample CLI Plugin](https://github.com/kubernetes/sample-cli-plugin) for an example usage of the tools provided in the CLI Runtime repo.
-->
参见 [CLI 插件示例](https://github.com/kubernetes/sample-cli-plugin)了解 CLI Runtime 存储库中提供的工具的使用示例。




## . heading "whatsnext" %}}


<!--
* Check the Sample CLI Plugin repository for [a detailed example](https://github.com/kubernetes/sample-cli-plugin) of a plugin written in Go.
* In case of any questions, feel free to reach out to the [CLI SIG team](https://github.com/kubernetes/community/tree/master/sig-cli).
* Binary plugins are a beta feature, so this is the time to contribute ideas and improvements to the codebase. We're also excited to hear about what you're planning to implement with plugins, so [let us know](https://github.com/kubernetes/community/tree/master/sig-cli)!
-->

* 查看 CLI 插件库示例，查看用 Go 编写的插件的[详细示例](https://github.com/kubernetes/sample-cli-plugin)
* 如有任何问题，请随时联系 [CLI SIG 小组](https://github.com/kubernetes/community/tree/master/sig-cli)
* 二进制插件是 beta 版的特性，所以现在是时候为代码库贡献一些想法和改进了。我们也很高兴听到您计划用插件实现什么，所以[让我们知道](https://github.com/kubernetes/community/tree/master/sig-cli)！




