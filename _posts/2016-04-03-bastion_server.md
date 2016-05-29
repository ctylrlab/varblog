---
layout: post
title: bastionサーバを導入する
---

bation(踏み台)サーバの導入メモ  

### bastionサーバとは何か

新しくウェブアプリケーションを用意する際にアプリケーションサーバでSSH処理を行うのはセキュリティ上ドキドキするということで、この認証処理は別サーバに分けてしまおうというのが基本的な考え方かと思います。

それでは実際にbationサーバを導入してみます。

### 導入方法

まずはサーバを用意します。  
今回はaws環境で行うので、appとbastion用に２台のインスタンスを用意します。

bationサーバはsshのみできれば大丈夫  
セキュリティグループで22番ポートのみinboudを許可します

次にapp用のサーバの設定をします  
このサーバではhttp(s)用のアクセスと内部向けIPに絞った22番ポートのinboudを許可します  
これで外部から直接appサーバにsshはできなくなったはず  

ただし、このままだとappサーバへsshするのが面倒なので、sshconfigに設定を追記します  

{% highlight shell %}
Host bastion
  Hostname <ホストのIP>
  User <ユーザ名>
  IdentityFile <鍵ファイル>

Host web
  Hostname <ホストのIP>
  User <ユーザ名>
  IdentityFile <鍵ファイル>
  ProxyCommand ssh -F <sshconfig> bastion -W %h:%p
{% endhighlight %}

appサーバにアクセスしたい時は

{% highlight shell %}
ssh -F <sshconfig> web
{% endhighlight %}

これで、無事にbastionサーバ経由でappサーバにログインできました。

--- 
