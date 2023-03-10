# IronMQ 和 Laravel:实现

> 原文：<https://www.sitepoint.com/ironmq-laravel-implementation/>

欢迎回到 IronMQ 和 Laravel 系列——这是第二部分，也是最后一部分，我们将最终完成支持后台作业的 web 应用程序。

已经有几个关于队列的教程——例如:[https://vimeo.com/64703617](https://vimeo.com/64703617)泰勒教你如何使用队列写文件。在本教程中，我们将尝试一些不同的东西。

我们将制作一个包含`job_id`和作业状态的**作业**表。当您将一个作业放入队列时，作业状态将是**排队**，当我们收到作业时，我们将状态设置为**运行**。因此，在完成后，我们会将其标记为**已完成**。

稍后，我们将调整图像的大小。

#### 作业表

#### 第一步

让我们创建一个作业表:

```
php artisan migrate:make create_jobs_table 
```

转到`app/database/migrations/xxxxx_create_jobs_table.php`并添加以下内容(该代码摘自`app/database/migrations/xxxxx_create_jobs_table.php`，因此请相应地编辑您的文件):

```
public function up()
{
    Schema::create('jobs', function($table)
{
    $table->increments('id');
    $table->string('job_id');
    $table->enum('status', array('queued', 'running','finished'))->default('queued');
    $table->timestamps();
    });
}

public function down()
{
    Schema::drop('jobs');
} 
```

这里我们创建了一个包含列`job_id`、`status`和`timestamps`的表格。运行`php artisan migrate`创建表格。

#### 第二步

在`app/models`中创建一个文件`job.php`，内容如下:

```
<?php

class Job extends Eloquent {

    protected $table = 'jobs';

    protected $fillable = array('job_id', 'status');

} 
```

使用以下命令创建 JobController:

```
php artisan controller:make JobController 
```

并在`app/routes.php`文件中添加以下内容:

```
Route::resource('job','JobController'); 
```

转到 JobController 填充**索引**方法。

```
public function index()
{
    $jobs = Job::all();

    return View::make('job.index')->with('jobs',$jobs);
} 
```

在我们继续之前，我们需要建立我们的观点。首先，让我们创建一个`views/master.blade.php`作为模板。

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Laravel PHP Framework</title>
    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css">
</head>
<body>

    <div class="container">
        <div class="row">
            <div class="col-md-12 well">
                Let's Learn Queues
            </div>

            @yield('content')
        </div>
    </div>

</body>
</html> 
```

让我们为我们的工作做一个展望。将以下内容放入`app/views/job/index.blade.php`

```
@extends('master')

@section('content')
<div class="col-md-12">
    <table class="table">
        <thead>
            <tr>
                <th>#</th>
                <th>Job ID</th>
                <th>Status</th>
            </tr>
        </thead>

        <tbody>
            @foreach($jobs as $job)
                <tr>
                    <td>{{ $job->id }}</td>
                <td>{{ $job->job_id }}</td>
                <td>{{ $job->status }}</td>
                </tr>
            @endforeach
        </tbody>

    </table>
</div>
@stop 
```

由于我们现在没有工作，如果我们去`http://localhost:8000/job`就什么都看不到了。

现在，让我们创建一个新的作业(将一个字符串写入文件)，然后将作业的状态更新为“finished”。

```
public function create()
{
    $job_id = Queue::push('JobController@run',array('string' => 'Hello World '));

    Job::create(array('job_id' => $job_id));

    return Redirect::route('job.index');
} 
```

注意，上面我们用一些数据将一个方法“run”推送到队列中。所以我们需要在 JobController 中创建一个方法“run”。

```
public function run($job,$data)
{

    $job_id = $job->getJobId(); // Get job id

    $ejob = Job::where('job_id',$job_id)->first(); // Find the job in database

    $ejob->status = 'running'; //Set job status to running

    $ejob->save();

    File::append('public/queue.txt',$data['string'].$job_id.PHP_EOL); //Add content to file

    $ejob->status = 'finished'; //Set job status to finished

    $ejob->save();

    return true;
} 
```

此时，您可以对此进行测试。让我们试一试由 [ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) 生成的 url。**请注意，你的网址将不同于我的。**

1.  去[http://953ffbb.ngrok.com](http://953ffbb.ngrok.com)(应该能用)
2.  在 Iron MQ Dashboard-> Project-> Queues-> Your Queues-> Push Queues 中:你应该在 subscribers 下看到[http://953ffbb.ngrok.com/queue/receive](http://953ffbb.ngrok.com/queue/receive)。
3.  转到[http://953ffbb.ngrok.com/job](http://953ffbb.ngrok.com/job):它应该显示一个工作列表(有可能没有工作，因为我们没有创建任何工作)。
4.  创造一份工作:[http://953ffbb.ngrok.com/job/create](http://953ffbb.ngrok.com/job/create)。这将创建一个作业并返回到 http://953ffbb.ngrok.com/job 的，在这里您将看到您的作业，其 job_id 和状态为“排队”。
5.  刷新页面:[http://953ffbb.ngrok.com/job](http://953ffbb.ngrok.com/job)，你可以看到状态变为‘已完成’，并找到带有一些文本的文件`public/queue.txt`。

就是这样！我们将一个作业推入队列，Iron 将作业发送给订户，我们完成了作业(将数据写入文件并更改状态)。

* * *

#### 调整照片大小

让我们上传照片，并使用干预调整它们的大小。

制作一个光控器来处理照片操作:

```
php artisan controller:make PhotoController 
```

在 *app/routes.php* 中为照片添加路线

```
Route::resource('photo','PhotoController'); 
```

让我们为照片做一张桌子:

```
php artisan migrate:make create_photos_table 
```

打开文件`app/database/migrations/xxxxxx_create_photos_table.php`和以下内容:

```
public function up()
{
    Schema::create('photos', function($table)
{
    $table->increments('id');

        $table->string('path');

        $table->timestamps();
    });
}

public function down()
{
    Schema::drop('photos');
} 
```

然后运行`php artisan migrate`来创建表。

让我们在`app/models/photo.php`为照片创建一个模型

```
<?php

class Photo extends Eloquent {

    protected $table = 'photos';

    protected $fillable = array('path');

} 
```

在光控制器中添加索引方法

```
public function index()
{
    $photos = Photo::all();

    return View::make('photo.index')->with('photos',$photos);
} 
```

在`app/views/photo/index.blade.php`中添加照片索引视图

```
@extends('master')

@section('content')
<div class="col-md-12">
    <h2>Photos</h2>
    <a href="{{ route('photo.create') }}" class="btn btn-primary">Upload New Photo</a> <br><br>
    <table class="table">
        <thead>
            <tr>
                <th>#</th>
                <th>Path</th>
            </tr>
        </thead>

        <tbody>
            @foreach($photos as $photo)
                <tr>
                    <td>{{ $photo->id }}</td>
                <td>{{ $photo->path }}</td>

                    <td>
                        <a href="{{ route('photo.edit', $photo->id) }}" class="btn btn-primary btn-xs"> Resize </a>
                    </td>
                </tr>
            @endforeach
        </tbody>

    </table>
</div>
@stop 
```

现在可以访问:[http://localhost:8000/photo](http://localhost:8000/photo)查看页面(但是不会有照片)

我们需要一个页面来上传照片，所以让我们创建一个视图并挂接一些方法来实现这一点。

将此内容放入`app/views/photo/create.blade.php`

```
@extends('master')

@section('content')
<div class="col-md-12">
    <h2>Upload New Photo</h2>

    {{ Form::open(array('route' => array('photo.store'),'files' => true)) }}

    <div class="form-group">
        {{ Form::file('photo') }}
    </div>

    {{ Form::submit('Upload',array('class' => 'btn btn-blue btn-primary')) }}

    {{ Form::close() }}

</div>
@stop 
```

在`app/controllers/PhotoController.php`中进行更改

```
public function create()
{
    return View::make('photo.create');
}

public function store()
{
    $file = Input::file('photo'); // Get the file

    $extension = $file->getClientOriginalExtension(); //Get the extension

    $filename = time().'.'.$extension; //Prepare filename

    $upload_success = $file->move('public/uploads', $filename); //Move file

    Photo::create(array('path' => 'uploads/'.$filename )); //Store info in DB

    return Redirect::route('photo.index');
} 
```

转到`http://localhost:8000/photo`，点击“上传新照片”并上传照片。你可以去`public/uploads`找到你的照片。同时，您可以找到照片列表以及一个调整大小按钮。现在，我们需要一个表单，其中我们可以提到宽度和高度来调整大小。

将此内容放入`app/views/photo/edit.blade.php`

```
@extends('master')

@section('content')
<div class="col-md-12">
    <h2>Resize Photo</h2>

    {{ Form::open(array('route' => array('photo.update',$photo->id), 'method' => 'PUT')) }}

    <div class="form-group">
        {{ Form::label('width', 'Width') }}

        {{ Form::text('width') }}
    </div>

    <div class="form-group">
        {{ Form::label('height', 'Height') }}

        {{ Form::text('height') }}
    </div>

    {{ Form::submit('Submit',array('class' => 'btn btn-blue btn-primary')) }}

    {{ Form::close() }}

</div>
@stop 
```

以下三种方法将完成我们调整图像大小的工作:

```
 public function edit($id)
    {
        $photo = Photo::find($id);

        return View::make('photo.edit')->with('photo', $photo);
    }

    public function update($id)
    {
        $data['photo_id'] = $id;
        $data['width'] = Input::get('width');
        $data['height'] = Input::get('height');

        $job_id = Queue::push('PhotoController@resize', $data); //Put a job in queue to resize

        Job::create(array('job_id' => $job_id));

        return Redirect::route('photo.index');
    }

    public function resize($job, $data)
    {

        $job_id = $job->getJobId(); // Get job id

        $ejob = Job::where('job_id', $job_id)->first(); // Find the job in database

        $ejob->status = 'running'; //Set job status to running

        $ejob->save();

        $photo = Photo::find($data['photo_id']);

        $filename = $data['width'] . '_' . $data['height'] . '_' . basename($photo->path);

        Image::open('public/' . $photo->path)
            ->resize($data['width'], $data['height'], true)
            ->save('public/uploads/' . $filename);

        Photo::create(array('path' => 'uploads/' . $filename));

        $ejob->status = 'finished'; //Set job status to finished

        $ejob->save();

        return true;
    }
```

现在，如果您点击 resize 并提交表单，将会有一个作业排队。如果你刷新照片页面，你会发现新的照片。

## 结论

在本系列文章中，我们用 Laravel 实现了 IronMQ，为我们的 web 应用程序添加了背景图像处理功能。看到多简单了吗？你有什么问题吗？想看更多吗？让我们知道！

## 分享这篇文章