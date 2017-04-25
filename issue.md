## 1. sublime 安装zencoding(emmet)
安装 Package Ctrol： 使用 ctrl + ～ 打开控制台，输入以下代码
```
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp =   sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```
在 Package ctrol（Ctrl+shift+P） 中选择 Install package，搜索 emmet 并安装。



##test for Gerrit
