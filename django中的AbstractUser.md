---


---

<p>Django中提供了一个AbstractUser类，我们可以用来自由的定制我们需要的model</p>
<p>首先导入AbstractUser</p>
<pre><code>from django.contrib.auth.models import AbstractUser
然后往其中添加gender和memo列，即可在Django的基础上添加我们所需要的信息。
class UserProfile(AbstractUser):
    '''
    继承Django的AbstractUser 并向里面添加两条数据内容
    '''
    gender = models.CharField(max_length=6,choices=(('male','男'),('female','女')),default='female',verbose_name='性别')
    memo = models.TextField(null=True, blank=True,verbose_name='便签')
    class Meta:
        verbose_name = '用户信息'
        verbose_name_plural = verbose_name #指定模型的复数形式是什么,如果不指定Django会自动在模型名称后加一个’s’
</code></pre>
<p>在完成后需要在settings.py中设置</p>
<pre><code>AUTH_USER_MODEL = 'app.UserProfile'
来覆盖Django默认的表
</code></pre>
<p>在admin.py中注册UserProfile表</p>
<pre><code>from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import UserProfile
 

# Register your models here.
admin.site.register(UserProfile,UserAdmin)#用UserAdmin去注册UserProfile
</code></pre>

