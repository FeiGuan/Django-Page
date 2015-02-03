# Django tutorial

###1. Install Django
####Using Virtual Environment
Create VE:
<pre>
virtualenv somename
</pre>
Activate VE:
<pre>
cd somename/
source bin/activate
</pre>
then you know you're in VE when you see the virtual env name before $<br>
install django:
<pre>
pip install django==1.4.5
</pre>
Now try
<pre>
pip freeze
</pre>
You'll see django is installed in this VE<br>
How to leave the VE:
<pre>
deactivate
</pre>

####Not using virtual environment
<pre>
sudo pip install django
</pre>
You can add version or add upgrade

###2. Build Models
Create App:
<pre>
python admin.py startapp signups
</pre>

Add new model:
```python
	last_name = models.CharField(max_length=120, null=True, blank=True)
	email = models.EmailField()
	timestamp = models.DateTimeField(auto_now_add=True, auto_now=False)
```
Synchronize Db:
<pre>
python manage.py syncdb
</pre>
Add admin (in /signups/admin.py):
```python
class SignUpAdmin(admin.ModelAdmin):
	class Meta:
		model = SignUp
```
Add it to site:
```python
admin.site.register(SignUp, SignUpAdmin)
```

###3. Views
Specify template file directory (in /settings.py):
```python
TEMPLATE_DIRS = (
	os.path.join(os.path.dirname(BASE_DIR), "static", "templates")
)
```
Create file: static/templates/signup.html<br>
Add URLs (in /urls.py)):
```python
urlpatterns = patterns('',
	url(r'^$', 'singups.views.home', name='home'),
	url(r'^admin/', include(admin.site.urls)),
)
```
Render data on html (the html files are already in TEMPLATE_DIRS):
```python
def home(request):
	return render_to_response("signup.html")
```
Add form model (/signups/forms.py), which is the same with SignUp model:
```python
from .models import SignUp
class SignUpForm(forms.ModelForm):
	class Meta:
		model = SignUp
```
Add the form model to view:
```python
from .forms import SignUpForm
def home(request):
	form = SignUpForm()
```
then form is a local variable of SignUpForm<br>
<br>
Add the form to page (form.as_p: render a variable in a paragraph):
<pre>
	<form method='POST' action=''>
		{{form.as_p}}
		<input type='submit'>
	</form>
</pre>
Add csrf_token
<pre>
{% crsf_token %}
</pre>
why is there nothing changed when submitted, because form is not a POST object<br>
<br>
form should inheritate request.POST
```python
form = SignUpForm(request.POST or None)
if form.is_valid():
	save_it = form.save(commit=False)
	save_it.save()
```
###4. Static files
Add static file directory for debugging (in settings.py)
```python
if DEBUG:
    MEDIA_URL = '/media/'
    STATIC_ROOT = os.path.join(os.path.dirname(BASE_DIR), "static", "static_only")
    MEDIA_ROOT = os.path.join(os.path.dirname(BASE_DIR), "static", "media")
    STATICFILES_DIRS = (
        os.path.join(os.path.dirname(BASE_DIR), "static", "static")
    )
```
Add static directory to urls:
```python
from django.conf import settings
from django.conf.urls.static import static
if settings.DEBUG:
	urlpatterns += static(settings.STATIC_URL, 
				document_root=settings.STATIC_ROOT)
	urlpatterns += static(settings.MEDIA_URL,
				document_root=settings.MEDIA_ROOT)
```
Collect the current static files to STATIC_ROOT
<pre>
python manage.py collectstatic
</pre>

###5. Frontend Tweak
Use base page:
<pre>
{% extends 'base.html' %}
</pre>
Content changed:
<pre>
{% block content-name %}
	content
{% endblock%}
</pre>

###6. Messaging
Add messages response to urls:
```python
from django.contrib import messages
def home(request):
	messages.success(request, 'Thank you')
```
Add messages to template:
<pre>
{% if messages %}
	{% for message in messages %}
		{{message}}
	{% endfor %}
{% endif %}
</pre>
###7. HTTP Redirect
Add responseRedirect (in /signups/views.py):
```python
from django.shortcurs import HttpResponseRedirect
def home(request):
	return HttpResponseRedirect('/thank-you/')
```
