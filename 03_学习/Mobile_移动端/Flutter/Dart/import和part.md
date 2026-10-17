简要区别：
  - import：引入另一个库。两个文件是不同库，私有成员（以下划线开头）不能互相访问，需要通过公开 API。
  - part：把文件拆成同一个库的多个文件。part 文件里必须写 part of '主库文件.dart';，它们共享同一库作用域，私有成员也能互相访问。

  用途：

  - import 用于依赖别的模块/包（常见、推荐）。
  - part 用于把一个库拆成多个文件，保持“同一库、同一作用域”。

  小例子：

  // a.dart
  library my_lib;
  part 'b.dart';

  void _secret() {}

  // b.dart
  part of 'a.dart';
  void callSecret() => _secret(); // 可以访问

  // c.dart
  import 'a.dart';
  void test() => _secret(); // ❌ 访问不到，因为不同库

  一句话记住：import 是“引用别的库”，part 是“拆同一个库”。
