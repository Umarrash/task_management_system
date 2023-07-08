'csrfmiddlewaretoken': '{{ csrf_token }}'
django-admin startproject mysite
python manage.py startapp polls
python manage.py runserver

settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    
    'demotest',
    
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'employees',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
------------------
Application- admin.py
from django.contrib import admin
from .models import Employee

# Register your models here.
admin.site.register(Employee)
------------------------
application - models.py
from django.db import models

# Create your models here.

class Employee(models.Model):
    name = models.CharField(max_length=50)
    email = models.CharField(max_length=30)
    phone = models.IntegerField(default=0)
    country = models.CharField(max_length=50)

    # class Meta:
    #     verbose_name_plural = "Employee"

python manage.py makemigrations demotest

python manage.py migrate
---------------------------------

project- url.py

from django.contrib import admin
from django.urls import path,include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('demotest.urls')),
]
----------------------------------
application- url.py

from django.urls import path
from . import views

urlpatterns = [
   
    path('', views.Index),
    path('getemployees/', views.getEmployee),
    path('create/', views.Create),
    path('edit_employee/<int:id>/', views.Edit),
    path('update_employee/<int:id>/', views.Update_emp),
    path('delete_employee/<int:id>/', views.Delete_emp),
    ]

    -----------------------------------------
    views.py
from django.shortcuts import render
from django.http import HttpResponse
from .models import Employee
from django.views.decorators.csrf import csrf_exempt
from django.http import JsonResponse

# Create your views here.

def Index(request):
    
    #return HttpResponse("hi this is umar")
    return render(request, 'index.html')

def getEmployee(request):
    
    #return HttpResponse("hi this is umar")
    allemployee = Employee.objects.values().order_by('id')
    print(allemployee)
    employee_data = list(allemployee)
    return JsonResponse({'allemployee':employee_data, 'status':200})
#HttpResponse("hi this is anas")

    
def Create(request):
    employee = Employee()
    employee.name = request.POST['name']
    employee.email = request.POST['email']
    employee.phone = request.POST['phone']
    employee.country = request.POST['country']
    employee.save()

    return JsonResponse({'message':'Employee created successfully', 'status':200})
    # return render(Request, 'index.html')

def Edit(request,id):
    employee = Employee.objects.filter(pk=id).values()
    employee_datas = list(employee)
    return JsonResponse({'employee_data':employee_datas[0], 'status':200})
    

def Update_emp(request,id):
    employee = Employee.objects.filter(pk=id).first()
    # return HttpResponse(employee.name)
    employee.name = request.POST['name']
    employee.email = request.POST['email']
    employee.phone = request.POST['phone']
    employee.country = request.POST['country']
    employee.save()
    return JsonResponse({'message':'Employee updated successfully', 'status':200})

def Delete_emp(request,id):
    employee = Employee.objects.get(pk=id)
    employee.delete()
    #employee_datas = list(employee)
    return JsonResponse({ 'status':200})

    ------------------------
html

<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Employee Management</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-9ndCyUaIbzAi2FUVXJi0CjmCapSmO7SnpJef0486qhLnuZ2cdeRhO02iuK6FUUVM" crossorigin="anonymous">
    
</head>
  <body>
    
    <div class="modal fade" id="AddTaskModal" tabindex="-1" aria-labelledby="AddTaskModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="AddTaskModalLabel">Add New Employee</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">

                    <ul id="save_msgList"></ul>

                    <div class="form-group mb-3">
                        <label for="">Name</label>
                        <input type="text" required class="name form-control">
                        <span id="name_span"></span>
                    </div>
                    <div class="form-group mb-3">
                        <label for="">Email</label>
                        <input type="text" required class="email form-control">
                    </div>
                    <div class="form-group mb-3">
                        <label for="">Phone</label>
                        <input type="text" required class="phone form-control">
                    </div>
                    <div class="form-group mb-3">
                        <label for="">Country</label>
                        <input type="text" required class="country form-control">
                    </div>

                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                    <button type="button" class="btn btn-primary add_task">Save</button>
                </div>

            </div>
        </div>
    </div>
   


    
    <div class="modal fade" id="editModal" tabindex="-1" aria-labelledby="editModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="editModalLabel">Edit & Update Task</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>

                <div class="modal-body">

                    <ul id="update_msgList"></ul>

                    <input type="hidden" id="task_id" />

                    <div class="form-group mb-3">
                        <label for="">Name</label>
                        <input type="text" id="name" required class="form-control">
                    </div>
                    <div class="form-group mb-3">
                        <label for="">Email</label>
                        <input type="text" id="email" required class="form-control">
                    </div>
                    <div class="form-group mb-3">
                        <label for="">Phone</label>
                        <input type="text" id="phone" required class="form-control">
                    </div>
                    <div class="form-group mb-3">
                        <label for="">Country</label>
                        <input type="text" id="country" required class="form-control">
                    </div>

                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                    <button type="submit" class="btn btn-primary update_task">Update</button>
                </div>

            </div>
        </div>
    </div>
   

   
    <div class="modal fade" id="DeleteModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">Delete Task</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <h4>Confirm to Delete Task ?</h4>
                    <input type="hidden" id="deleteing_id">
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                    <button type="button" class="btn btn-primary delete_task">Yes Delete</button>
                </div>
            </div>
        </div>
    </div>
    


    
    <div class="container py-5">
        <div class="row">
            <div class="col-md-12">

                <div id="success_message"></div>

                <div class="card">
                    <div class="card-header">
                        <h4>
                            Employee List
                            <button type="button" class="btn btn-primary float-end" data-bs-toggle="modal"
                                data-bs-target="#AddTaskModal">Add Employee</button>
                        </h4>
                    </div>
                    <div class="card-body">
                        <table class="table table-bordered">
                            <thead>
                                <tr>
                                    <th>ID</th>
                                    <th>Name</th>
                                    <th>Email</th>
                                    <th>Phone</th>
                                    <th>Country</th>
                                    <th>Action</th>

                                </tr>
                            </thead>
                            <tbody>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
    

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js" integrity="sha384-geWF76RCwLtnZ8qwWowPQNguL3RmwHVBC9FhGdlKrxdiJJigb/j/68SIy3Te4Bkz" crossorigin="anonymous"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
</body>
</html>

<script>

    $(document).ready(function () {
console.log('presrent');
        fetchtask();
        });
        function fetchtask() {
            $.ajax({
                type: "GET",
                url: "getemployees/",
                dataType: "json",
                success: function (response) {

                    $('tbody').html("");
                    $.each(response.allemployee, function (key, item) {
                        $('tbody').append('<tr>\
                            <td>' + item.id + '</td>\
                            <td>' + item.name + '</td>\
                            <td>' + item.email + '</td>\
                            <td>' + item.phone + '</td>\
                            <td>' + item.country + '</td>\
                            <td><button type="button" value="' + item.id + '" class="btn btn-primary editbtn btn-sm">Edit</button>\
                            <button type="button" value="' + item.id + '" class="btn btn-danger deletebtn btn-sm">Delete</button></td>\
                        \</tr>');
                    });
                }
            });
        }
        

    


    $(document).on('click', '.add_task', function (e) {
        e.preventDefault();

        $(this).text('Sending..');

        var data = {
            'name': $('.name').val(),
            'email': $('.email').val(),
            'phone': $('.phone').val(),
            'country': $('.country').val(),
            'csrfmiddlewaretoken': '{{ csrf_token }}'
        }
        if(data.name == ""){
            $('#name_span').html("Name is required").css("color", "red");;
        }
        //alert(data.name);

        $.ajaxSetup({
            headers: {
                'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
            }
        });

        $.ajax({
            type: "POST",
            url: "create/",
            data: data,
            dataType: "json",
            success: function (response) {
                // console.log(response);
                if (response.status == 400) {
                    $('#save_msgList').html("");
                    $('#save_msgList').addClass('alert alert-danger');
                    $.each(response.errors, function (key, err_value) {
                        $('#save_msgList').append('<li>' + err_value + '</li>');
                    });
                    $('.add_task').text('Save');
                } else {
                    $('#save_msgList').html("");
                    $('#success_message').addClass('alert alert-success').slideUp(6000);
                    $('#success_message').text(response.message);
                    $('#AddTaskModal').find('input').val('');
                    $('.add_task').text('Save');
                    $('#AddTaskModal').modal('hide');
                    fetchtask();
                }
            }
        });
    });
    
    $(document).on('click', '.editbtn', function (e) {
        e.preventDefault();
        var employee_id = $(this).val();

        $('#editModal').modal('show');
        $.ajax({
            type: "GET",
            url: "/edit_employee/" + employee_id+"/",
            success: function (response) {
                if (response.status == 404) {
                    $('#success_message').addClass('alert alert-success');
                    $('#success_message').text(response.message);
                    $('#editModal').modal('hide');
                } else {
                    
                    $('#name').val(response.employee_data.name);
                    $('#email').val(response.employee_data.email);
                    $('#phone').val(response.employee_data.phone);
                    $('#country').val(response.employee_data.country);
                    $('#task_id').val(employee_id);
                }
            }
        });
        $('.btn-close').find('input').val('');

    });


    $(document).on('click', '.update_task', function (e) {
        e.preventDefault();

        $(this).text('Updating..');
        var id = $('#task_id').val();

        var data = {
            'name': $('#name').val(),
            'email': $('#email').val(),
            'phone': $('#phone').val(),
            'country': $('#country').val(),
            'csrfmiddlewaretoken': '{{ csrf_token }}'
        }

        $.ajax({
            type: "POST",
            url: "/update_employee/" + id+"/",
            data: data,
            dataType: "json",
            success: function (response) {
                // console.log(response);
                if (response.status == 400) {
                    $('#update_msgList').html("");
                    $('#update_msgList').addClass('alert alert-danger');
                    $.each(response.errors, function (key, err_value) {
                        $('#update_msgList').append('<li>' + err_value +
                            '</li>');
                    });
                    $('.update_task').text('Update');
                } else {
                    $('#update_msgList').html("");

                    $('#success_message').addClass('alert alert-success');
                    $('#success_message').text(response.message);
                    $('#editModal').find('input').val('');
                    $('.update_task').text('Update');
                    $('#editModal').modal('hide');
                    fetchtask();
                }
            }
        });

    });



    $(document).on('click', '.deletebtn', function () {
        var task_id = $(this).val();
        $('#DeleteModal').modal('show');
        $('#deleteing_id').val(task_id);
    });



    $(document).on('click', '.delete_task', function (e) {
        e.preventDefault();

        $(this).text('Deleting..');
        var id = $('#deleteing_id').val();
        var data = {
        'id' : $('#deleteing_id').val(),
        'csrfmiddlewaretoken': '{{ csrf_token }}'
        }

        $.ajax({
            type: "POST",
            url: "/delete_employee/" + id+"/",
            data: data,
            //csrfmiddlewaretoken: '{{ csrf_token }}'
            dataType: "json",
            success: function (response) {
                // console.log(response);
                if (response.status == 404) {
                    $('#success_message').addClass('alert alert-success');
                    $('#success_message').text(response.message);
                    $('.delete_task').text('Yes Delete');
                } else {
                    $('#success_message').html("");
                    $('#success_message').addClass('alert alert-success');
                    $('#success_message').text(response.message);
                    $('.delete_task').text('Yes Delete');
                    $('#DeleteModal').modal('hide');
                    fetchtask();
                }
            }
        });
    });

    
</script>

-------------
    
