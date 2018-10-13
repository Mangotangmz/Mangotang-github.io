---


---

<h1 id="前言">前言</h1>
<p>在flask中如何快速的实现登录注册，注销功能，以及登录状态等功能？flask的扩展库 flask-login可以快速的实现功能。</p>
<h2 id="安装flask—login">1. 安装flask—login</h2>
<pre><code>pip install flask-login
</code></pre>
<h2 id="实现登录注册功能">2.实现登录注册功能</h2>
<h3 id="定义login.html模板">2.1 定义login.html模板</h3>
<pre><code>{% extends 'base.html' %}

{% block title %}
    登录页面
{% endblock %}

{% block content %}
    &lt;form action="" method="post"&gt;
        姓名:&lt;input type="text" name="username"&gt;
        密码:&lt;input type="text" name="password"&gt;
        &lt;input type="submit" value="提交"&gt;
    &lt;/form&gt;
{% endblock %}

</code></pre>
<h2 id="实现登录功能"># 2.2 实现登录功能</h2>
<p>登录方法中定义被login_manager.user_loader装饰的回调函数，回调函数在如下两个地方调用：</p>
<ol>
<li>该函数表明当前用户登录成功是调用login_user方法时，会被回调的函数。回调函数实现的功能是向会话上下问session中存储为中间的键值对，key为user_id,value为当前登录用户的ID值。</li>
<li>回调函数在访问任何一个路由地址时都会被调用。<br>
注意：</li>
</ol>
<pre><code>因为请求上下文每次在建立连接时，都需要获取当前登录用户并将当前登录用户设置为全局上下文current_user,因此回调函数返回的是当前登录一同的用户对象。
</code></pre>
<pre><code>form flask_login improt LoginManager, login_required, login_user, logout_user, current_user
#获取管理对象
login_user = LoginManager()

@login_manager.user_loder
def load_user(user_id):
   #必须编写一个函数用户从数据库加载用户
   #这个函数在login_user(user)存储当前登录用户到session中是，会被调用
   #在每次访问地址的时候都会被调用，用于向请求上下文中绑定当前登录的用户信息
   return User.query.get(uer_id)



@blue.route('/login/', methods=['GET', 'POST'])
def login():
   if request.method == 'GET':
       return render_template('login.html')

   if request.method == 'POST':
       username = request.form.get('username')
       password = request.form.get('password')
       # 校验用户名和密码是否填写完成
       if not all([username, password]):
           return render_template('login.html')
       # 通过用户名获取用户对象
       user = User.query.filter_by(username=username).first()
       # 校验密码是否正确
       if check_password_hash(user.password, password):
           # 实现登录
           # login_user()能够将已登录并通过load_user()的用户对应的User对象保存在session中
           # 在session中会创建一个键值对，key为user_id，value为当前登录用户的id值
           # 如果希望应用记住用户的登录状态, 只需要为 login_user()的形参 remember 传入 True 实参就可以了.
           login_user(user)
           return redirect(url_for('user.index'))
       else:
           flash('用户名或者密码错误')

       return redirect(url_for('user.index'))
</code></pre>
<h3 id="启动文件配置">2.3 启动文件配置</h3>
<p>session_protection:设置存储用户的安全级别<br>
login-view：设置登录验证失败的交转地址</p>
<pre><code>form user.view improt login_manager

app.config['SECRET_KEY']= os.urandom(24)

#登录管理，初始化app
#可以设置None， ‘base’， ‘strong’以提供不等的安全等级，一般设置strong，如果发现异常会登出用户
#session_protection 能够更好的防止恶意用户篡改cookies，当发现被篡改时，该童虎的session对象会被立即删除
login_manager.session_protection = 'stong'

# 当登录认证不通过，则跳转
login_manager.login_view = 'user.login'
login_manager.int_app(app)
</code></pre>
<h3 id="访问首页，登录校验">2.4 访问首页，登录校验</h3>
<p>使用装饰器login_required()进行登陆校验。<br>
核心思想：校验session中是否存在key为user_id的键值对，如果校验成功，则继续访问启动文件中定义的login_manager.login_view定义的视图函数</p>
<pre><code>@blue.route('/index/')
@login_required
def index():
    return render_template('index.html')
</code></pre>
<p>如果登录校验成功，则渲染index.html首页，在页面中可以解析全局变量current_user参数。</p>
<pre><code>{% extends 'base.html' %}

{% block title %}
    首页页面
{% endblock %}

{% block content %}
    &lt;p&gt;我是首页&lt;/p&gt;
    &lt;p&gt;当前登录系统用户为: {{ current_user.username }}&lt;/p&gt;
{% endblock %}
</code></pre>
<h3 id="注销">2.5 注销</h3>
<p>使用logout_user()方法实现注销，核心功能就是删除当前会话上下文session中的user_id键值对。</p>
<pre><code># 退出
</code></pre>
<p>@blue.route(’/logout/’, methods=[‘GET’])<br>
@login_required<br>
def logout():<br>
logout_user()<br>
return redirect(url_for(‘user.login’))</p>
<pre><code></code></pre>

