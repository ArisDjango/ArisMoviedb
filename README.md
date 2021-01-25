# ArisMoviedb

# Chapter 1:  MyMDB
## Aplikasi Movie Database (MyMDB)
- Memulai project
    - Instalasi Django
        - `python pip install django psycopg2`
        - atau jika mempunyai file requirements gunakan : `pip install -r requirements.dev.txt`
    - Membuat project
        - `django-admin startproject config`
        - `mv config django` --> rename root agar jadi 'django' agar ndak bingung
    
## Instalasi postgreSQL
- Instalasi di lokal
    - instal postgreSQL.exe
    - login sebagai super user: psql -U postgres -h localhost
    - \du -> untuk melihat data user saat ini
- Mengatur User/pass, Hak akses, membuat DB.
    ```
        CREATE DATABASE mymdb;
        CREATE USER aris;
        GRANT ALL ON DATABASE mymdb to "aris";
        ALTER USER aris PASSWORD 'aris1985';
        ALTER USER aris CREATEDB;
    ```

## Config database django
- Konfigurasi setting database
    - django/config/settings.py
    - Tentukan engine = postgres (defaultnya sqlite)
    - Tentukan name, user, pass, host, port
- Pastikan konfigurasi diatas sesuai dengan data waktu instalasi Postgres

## core app
- Pendahuluan
    - MVT
        - Models = Menyimpan dan mengambil data dari database
        - Views = memproses HTTP requests, mengolah operasi pada models, mengembalikan HTTP responses
        - Templates = Tampilan body respon
    - Struktur app
        - Saat ini folder 'django' sebagai folder config
        - di dalam django, kita bisa install app berapapun jumlahnya
        - selanjutnya kita buat 'core'
- Membuat core app
    - `cd django`
    - `python manage.py startapp core`
    - django/manage.py, tambahkan 'core' pada INSTALLED_APP

## core Models
- Membuat models – Movie()
    - django/core/models.py
    - buat `class Movie`, code:
- Migrasi the database
    - Menerapkan model database core:
    - `cd django`
    - `python manage.py makemigrations core`
    - `python manage.py migrate core`
    - Cek data tabel pada postgres (login sbg user 'aris') --> mymdb=> \dt
    - `python manage.py migrate`
- Membuat postingan ke database
    - cd django
    - python manage.py shell
    - post:
    ```
        from core.models import Movie

        sleuth = Movie.objects.create(
        title='Sleuth',
        plot='An snobbish writer who loves games'
        ' invites his wife\'s lover for a battle of wits.',
        year=1972,
        runtime=138,
        )
        sleuth.id
        1
        sleuth.get_rating_display()
        'NR - Not Rated'
    ```

## core Admin
- Membuat admin
    - django/core/admin.py
    - Register 'Movie Model' sebagai data awal yang diolah di admin panel :
        ```
            from django.contrib import admin
            from core.models import Movie
            admin.site.register(Movie)
        ```
    - Membuat superuser untuk admin
        ```
            cd django
            python manage.py createsuperuser
        ```
    - login 127.0.0.1:8000/admin, masuk sebaai superuser


## core Views        
- Membuat MovieList()
    - class ini akan menggenerate view sederhana dari model Movie
    - django/core/views.py
    - code:
        ```
            from django.views.generic import ListView
            from core.models import Movie

            class MovieList(ListView):
                model = Movie
        ```
## core templates/core
- templates akan otomatis dipanggil ketika view sudah terbentuk minimal 1 method as_view()
- core/templates/core
- template – movie_list.html
    - django/core/templates/core/movie_list.html
    - code:
## Routing
- django/core/urls.py 
    - path: localhost/movies --> menampilkan views.MovieList() 
- django/config/urls.py
    - path: localhost/admin
    - path: localhost/ --> core/urls
- Running the development server
  - cd django
  - python manage.py runserver

## Individual movie pages
- Creating the MovieDetail view

- Creating the movie_detail.html template
- Adding MovieDetail to core.urls.py
- A quick review of the section
## Pagination and linking movie list to movie details
- Updating MovieList.html to extend base.html
- Setting the order
- Adding pagination
- 404 – for when things go missing
- Testing our view and template
## Adding Person and model relationships
- Adding a model with relationships 44
- Different types of relationship fields 45
    - Director – ForeignKey 45
    - Writers – ManyToManyField 47
    - Role – ManyToManyField with a through class 47
- Adding the migration 48
- Creating a PersonView and updating MovieList 49
    - Creating a custom manager – PersonManager 49
    - Creating a PersonDetail view and template 51
    - Creating MovieManager 53
- A quick review of the section 53
## Summary

# Chapter 2: Adding Users to MyMDB
## Creating the user app 55
- Creating a new Django app 56
- Creating a user registration view 56
- Creating the RegisterView template 58
- Adding a path to RegisterView 60
- Logging in and out 61
    - Updating user URLConf 61
    - Creating a LoginView template 62
    - A successful login redirect 63
    - Creating a LogoutView template 63
- A quick review of the section

## Letting users vote on movies
- Creating the Vote model
- Creating VoteForm
- Creating voting views
    - Adding VoteForm to MovieDetail
    - Creating the CreateVote view
    - Creating the UpdateVote view
- Adding views to core/urls.py
- A quick review of the section
## Calculating Movie score
- Using MovieManager to calculate Movie score
- Updating MovieDetail and template
## Summary

# Chapter 3: Posters, Headshots, and Security
## Uploading files to our app
- Configuring file upload settings 78
- Creating the MovieImage model 80
- Creating and using the MovieImageForm 81
- Updating movie_detail.html to show and upload - - images 83
- Writing the MovieImageUpload view 84
- Routing requests to views and files 85
## OWASP Top 10
- A1 injection 87
- A2 Broken Authentication and Session Management 88
- A3 Cross Site Scripting 88
- A4 insecure direct object references 89
- A5 Security misconfiguration 89
- A6 Sensitive data exposure 90
- A7 Missing function level access control 90
- A8 Cross Site Request Forgery (CSRF) 90
- A9 Using components with known vulnerabilities 91
- A10 Unvalidated redirects and forwards 91
## Summary 92

# Chapter 4: Caching in on the Top 10 93
## Creating a top 10 movies list 93
- Creating MovieManager.top_movies() 93
- Creating the TopMovies view 94
- Creating the top_movies_list.html template 95
- Adding a path to TopMovies 96
## Optimizing Django projects 97
- Using the Django Debug Toolbar 97
- Using Logging 98
- Application Performance Management 99
- A quick review of the section 99
## Using Django's cache API 99
- Examining the trade-offs between Django cache backends 100
    - Examining Memcached trade-offs 100
    - Examining dummy cache trade-offs 101
    - Examining local memory cache trade-offs 101
    - Examine file-based cache trade-offs 102
    - Examining database cache trade-offs 103
- Configuring a local memory cache 103
- Caching the movie list page 104
    - Creating our first mixin CachePageVaryOnCookieMixin 105
    - Using CachePageVaryOnCookieMixin with MovieList 107
- Caching a template fragment with {% cache %} 107
- Using the cache API with objects 109
## Summary 110

# Chapter 5: Deploying with Docker 111
## Organizing configuration for production and development 111
- Splitting requirements files 112
- Splitting settings file 112
    - Creating common_settings.py 113
    - Creating dev_settings.py 113
    - Creating production_settings.py 115
## Creating the MyMDB Dockerfile 117
- Starting our Dockerfile 118
- Installing packages in Dockerfile 119
- Collecting static files in Dockerfile 119
- Adding Nginx to Dockerfile 120
    - Configuring Nginx 121
    - Creating Nginx runit service 122
- Adding uWSGI to the Dockerfile 122
- Configuring uWSGI to run MyMDB 122
    - Creating the uWSGI runit service 123
- Finishing our Dockerfile 124
## Creating a database container 125
## Storing uploaded files on AWS S3 125
- Signing up for AWS 126
- Setting up the AWS environment 126
- Creating the file upload bucket 127
## Using Docker Compose 128
- Tracing environment variables 130
- Running Docker Compose locally 131
    - Installing Docker 131
    - Using Docker Compose 131
## Sharing your container via a container registry 132
## Launching containers on a Linux server in the cloud 133
- Starting the Docker EC2 VM 134
- Shutting down the Docker EC2 VM 135
## Summary 136

# Chapter 6: Starting Answerly 137
## Creating the Answerly Django project 137
## Creating the Answerly models 139
- Creating the Question model 140
- Creating the Answer model 141
- Creating migrations 142
## Adding a base template 143
- Creating base.html 143
## Configuring static files 145
## Letting users post questions 146
- Ask question form 146
- Creating AskQuestionView 147
- Creating ask.html 148
- Installing and configuring Markdownify 150
- Installing and configuring Django Crispy Forms 151
- Routing requests to AskQuestionView 152
- A quick review of the section 153
## Creating QuestionDetailView 154
- Creating Answer forms 154
    - Creating AnswerForm 154
    - Creating AnswerAcceptanceForm 155
- Creating QuestionDetailView 156
- Creating question_detail.html 157
    - Creating the display_question.html common template 157
    - Creating list_answers.html 158
    - Creating the post_answer.html template 160
- Routing requests to the QuestionDetail view 160
## Creating the CreateAnswerView 161
- Creating create_answer.html 162
- Routing requests to CreateAnswerView 163
## Creating UpdateAnswerAcceptanceView 163
## Creating the daily questions page 164
- Creating DailyQuestionList view 164
- Creating the daily question list template 165
- Routing requests to DailyQuestionLists 166
## Getting today’s question list 166
## Creating the user app 167
- Using Django's LoginView and LogoutView 169
- Creating RegisterView 170
## Updating base.html navigation 171
## Running the development server 172
## Summary 172

# Chapter 7: Searching for Questions with Elasticsearch 173
## Starting with Elasticsearch 173
- Starting an Elasticsearch server with docker 174
- Configuring Answerly to use Elasticsearch 175
- Creating the Answerly index 176
## Loading existing Questions into Elasticsearch 176
- Creating the Elasticsearch service 176
- Creating a manage.py command 179
## Creating a search view 181
- Creating a search function 181
- Creating the SearchView 182
- Creating the search template 183
- Updating the base template 185
## Adding Questions into Elasticsearch on save() 186
- Upserting into Elasticsearch 187
## Summary 188

# Chapter 8: Testing Answerly 189
## Installing Coverage.py 189
## Measuring code coverage 190
## Creating a unit test for Question.save() 193
## Creating models for tests with Factory Boy 195
- Creating a UserFactory 196
- Creating the QuestionFactory 198
## Creating a unit test for a view 199
## Creating a view integration test 201
## Creating a live server integration test 203
- Setting up Selenium 204
- Testing with a live Django server and Selenium 204
## Summary 207

# Chapter 9: Deploying Answerly 208
## Organizing configuration for production and development 209
- Splitting our requirements file 209
- Splitting our settings file 210
    - Creating common_settings.py 210
    - Creating dev_settings.py 211
    - Creating production_settings.py 211
## Preparing our server 213
- Installing required packages 213
- Configuring Elasticsearch 214
    - Installing Elasticsearch 214
    - Running Elasticsearch 215
- Creating the database 216
## Deploying Answerly with Apache 216
- Creating the virtual host config 217
- Updating wsgi.py to set environment variables 219
- Creating the environment config file 220
- Migrating the database 220
- Collecting static files 221
- Enabling the Answerly virtual host 222
- A quick review of the section 222
## Deploying Django projects as twelve-factor apps 223
- Factor 1 – Code base 224
- Factor 2 – Dependencies 224
- Factor 3 – Config 225
- Factor 4 – Backing services 225
- Factor 5 – Build, release, and run 226
- Factor 6 – Processes 226
- Factor 7 – Port binding 227
- Factor 8 – Concurrency 227
- Factor 9 – Disposability 228
- Factor 10 – Dev/prod parity 228
- Factor 11 – Logs 229
- Factor 12 – Admin processes 229
- A quick review of the section 230
## Summary 230

# Chapter 10: Starting Mail Ape 231
## Creating the Mail Ape project 232
- Listing our Python dependencies 232
- Creating our Django project and apps 232
- Creating our app's URLConfs 233
- Installing our project's apps 235
## Creating the mailinglist models 236
- Creating the MailingList model 236
- Creating the Subscriber model 238
- Creating the Message model 239
## Using database migrations 240
- Configuring the database 241
- Creating database migrations 241
- Running database migrations 242
## MailingList forms 243
- Creating the Subscriber form 243
- Creating the Message Form 244
- Creating the MailingList form 245
## Creating MailingList views and templates 246
- Common resources 247
- Creating a base template 247
- Configuring Django Crispy Forms to use Bootstrap 4 251
- Creating a mixin to check whether a user can use the mailing list 251
- Creating MailingList views and templates 252
    - Creating the MailingListListView view 253
    - Creating the CreateMailingListView and template 255
    - Creating the DeleteMailingListView view 257
    - Creating MailingListDetailView 259
- Creating Subscriber views and templates 261
    - Creating SubscribeToMailingListView and template 262
    - Creating a thank you for subscribing view 264
    - Creating a subscription confirmation view 265
    - Creating UnsubscribeView 266
- Creating Message Views 268
    - Creating CreateMessageView 268
    - Creating the Message DetailView 272
## Creating the user app 273
- Creating the login template 275
- Creating the user registration view 276
## Running Mail Ape locally 277
## Summary 277

# Chapter 11: The Task of Sending Emails 278
## Creating common resources for emails 278
- Creating the base HTML email template 279
- Creating EmailTemplateContext 279
## Sending confirmation emails 281
- Configuring email settings 281
- Creating the send email confirmation function 282
- Creating the HTML confirmation email template 284
- Creating the text confirmation email template 285
- Sending on new Subscriber creation 286
- A quick review of the section 287
## Using Celery to send emails 287
- Installing celery 288
- Configuring Celery settings 289
- Creating a task to send confirmation emails 290
- Sending emails to new subscribers 292
- Starting a Celery worker 293
- A quick review of the section 293
## Sending messages to subscribers 294
- Getting confirmed subscribers 294
- Creating the SubscriberMessage model 295
- Creating SubscriberMessages when a message is created 297
- Sending emails to subscribers 298
## Testing code that uses Celery tasks 301
- Using a TestCase mixin to patch tasks 302
- Using patch with factories 304
- Choosing between patching strategies 306
## Summary 306

# Chapter 12: Building an API 307
## Starting with the Django REST framework 307
- Installing the Django REST framework 308
- Configuring the Django REST Framework 308
## Creating the Django REST Framework Serializers 310
## API permissions 314
## Creating our API views 315
- Creating MailingList API views 315
    - Listing MailingLists by API 316
    - Editing a mailing list via an API 317
- Creating a Subscriber API 318
    - Listing and Creating Subscribers API 319
    - Updating subscribers via an API 321
## Running our API 322
## Testing your API 325
## Summary 327

# Chapter 13: Deploying Mail Ape 328
## Separating development and production 328
- Separating our requirements files 328
- Creating common, development, and production settings 329
## Creating an infrastructure stack in AWS 332
- Accepting parameters in a CloudFormation template 333
- Listing resources in our infrastructure 334
    - Adding Security Groups 335
    - Adding a Database Server 336
    - Adding a Queue for Celery 338
    - Creating a Role for Queue access 339
- Outputting our resource information 341
- Executing our template to create our resources 341
## Building an Amazon Machine Image with Packer 344
- Installing Packer 345
- Creating a script to create our directory structure 345
- Creating a script to install all our packages 345
- Configuring Apache 346
- Configuring Celery 348
- Creating the environment configuration files 350
- Making a Packer template 351
- Running Packer to build an Amazon Machine Image 356
## Deploying a scalable self-healing web app on AWS 357
- Creating an SSH key pair 357
- Creating the web servers CloudFormation template 358
    - Accepting parameters in the web worker CloudFormation template 358
    - Creating Resources in our web worker CloudFormation template 359
    - Outputting resource names 362
- Creating the Mail Ape 1.0 release stack 363
- SSHing into a Mail Ape EC2 Instance 365
- Creating and migrating our database 366
- Releasing Mail Ape 1.0 368
- Scaling up and down with update-stack 368
## Summary 371
