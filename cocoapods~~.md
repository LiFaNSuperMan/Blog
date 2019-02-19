##Ruby - Cocoapods - xcconfig
* cocoapods原理晋级
	* Ruby一切皆对象
	* Ruby会定义一些方法例如target、pod、source等等解析podfile
	* pod install做的事情
		* 在cocoapods中，所有的命令都会由command类派发给对应的类，真正执行pod install的类是install。在config中会获取一个installer的实例，由installer执行install方法，这个类有一个属性是update，这个属性是pod update和install的关键区别，update方法会忽略podfile.lock文件，重新对依赖进行分析。
	``` ruby
	 module Pod
		  class Podfile
			module DSL
			  def pod(name = nil, *requirements) end
			  def target(name, options = nil) end
			  def platform(name, target = nil) end
			  def inhibit_all_warnings! end
			  def use_frameworks!(flag = true) end
			  def source(source) end
			  ...
			end
		  end
		end
	```
		- 其中部分方法如下：
	source方法
	``` ruby
	 def source(source)
			  hash_sources = get_hash_value('sources') || []
			  hash_sources << source
			  set_hash_value('sources', hash_sources.uniq)
		end
	```
	target方法
	``` ruby
		def target(name, options = nil)
			  if options
				raise Informative, "Unsupported options `#{options}` for " \
				  "target `#{name}`."
			  end
			  parent = current_target_definition
			  definition = TargetDefinition.new(name, parent)
			  self.current_target_definition = definition
			  yield if block_given?
			ensure
			  self.current_target_definition = parent
			end
	```
		* 总结一下，CocoaPods 对 Podfile 的解析与我们在前面做的手动解析 Podfile 的原理差不多，构建一个包含一些方法的上下文，然后直接执行 eval 方法将文件的内容当做代码来执行，这样只要 Podfile 中的数据是符合规范的，那么解析 Podfile 就是非常简单容易的。
	* Podfile 被解析后的内容会被转化成一个 Podfile 类的实例，而 Installer 的实例方法 install! 就会使用这些信息安装当前工程的依赖，而整个安装依赖的过程大约有四个部分：
		- 解析 Podfile 中的依赖
			- 主要是解除依赖关系，使用了一个milinillo的算法，主要原理是回溯和向前检查
			- [milinillo](https://github.com/CocoaPods/Molinillo/blob/master/ARCHITECTURE.md)	 
		- 下载依赖
			- 会创建下载器，根据不用的下载方式CocoaPods-Downloader
			- 大部分的依赖都会被下载到 ~/Library/Caches/CocoaPods/Pods/Release/ 这个文件夹中，然后从这个这里复制到项目工程目录下的 ./Pods 中，这也就完成了整个 CocoaPods 的下载流程。
		- 创建 Pods.xcodeproj 工程
			- 使用工具xcodeproj
			- 生成 Pods.xcodeproj 工程
			- 将依赖中的文件加入工程
			- 将依赖中的 Library 加入工程
			- 设置目标依赖（Target Dependencies）
		- 集成 workspace
			``` ruby 
				def install!
				  resolve_dependencies
				  download_dependencies
				  generate_pods_project
				  integrate_user_project
				end
			```	
	* 总结最后想说的是 pod install 和 pod update 区别还是比较大的，每次在执行 pod install 或者 update 时最后都会生成或者修改 Podfile.lock 文件，其中前者并不会修改 Podfile.lock 中显示指定的版本，而后者会会无视该文件的内容，尝试将所有的 pod 更新到最新版。
CocoaPods 工程的代码虽然非常多，不过代码的逻辑非常清晰，整个管理并下载依赖的过程非常符合直觉以及逻辑。

* cocoapods生成文件分析以及xcconfig
* 参考文档
	* [Cocoapods原理总结](https://juejin.im/entry/59dd94b06fb9a0451463030b)
	* [Cocoapods都做了什么](https://draveness.me/cocoapods) 
