# Aws-ElasticBeanstalk-Setup-Guide
Cheatsheet for AWS Elastic Beanstalk Rails 6 Setup </br>
- This Guide Presumes that you already have ample knowledge of the basics of working in rails, postgres, git and terminal commands

# Deploy Rails 6 Application to Elastic Beanstalk With RDS PostgresQL Database
  1) Set Up AWS Account, User & AWS cli
  2) Create RDS Database and Configure settings for inbound & outbound traffic
  3) Create Elastic Beanstalk Environment 
  4) Configure Rails Application with correct server dependencies for production and ruby version
  5) Set Up Environment variables on ElasticBeanstalk pointed at RDS instance
  6) Deploy Application using CLI
  7) Setup SSH to connect to RDS instance from your terminal to migrate and seed database using your new eb instance
  
Deploying a rails app to AWS can be tricky, especially if this is your first time. 
</br>
To start, you are going to need to ensure that you have a User created in the IAM manager of your AWS account and it has the proper permissions setup. 
</br>

# Setup AWS User
Go ahead and create an AWS account if you have not already.
</br>
Once logged in and on The main AWS Console, Click on the Services Navigation tab at the top and search for 'IAM' 
</br>
Select this Service
</br>
Once on the IAM manager page, select Users.
</br>
Click the 'Add Users' button in the top right hand corner
</br>
type in your name where it asks for a Username
<br>
check the box that says "Access key - Programmatic access"
</br>
This will create an access key ID and a Secret Access key for you to use to login to AWS using your local machine
<br/>
Click 'Next:Permissions'
<br/>
On the Next Page, it will ask you to add tags.
<br/>
Go ahead and add a tag so you can recognize this user later, Name:your-username
</br>
Click Download .csv
</br>
Save this csv file.
</br>
You will need these credentials to setup your cli to access your AWS account in order to create your Elastic Beanstalk application
</br>

Check the local ruby version and the ruby version you used to setup your rails application.
This is an important step that can be easy to mess up, so follow closely.
</br>
  In the root of your app directory, run: 
  </br>
  $ ruby -v
  </br>
  this will check the Ruby version you are running
  </br>
  the output will be similar to: 'ruby 2.7.4p191 (2021-07-07 revision a21a3b7d23) [arm64-darwin20]'
  </br>
  It is important to note the trailing text after your initial version, as you may need this for debugging your deployment later on.
  </br>
  Login to the AWS Console. 
  </br>
  Select 'Services' from the top left navigation bar
  </br>
  Under 'Compute' select 'Elastic Beanstalk'
  </br>
  - This will take you to the Elastic Beankstalk Console. 
  - From here, Select 'Create Application'
  - select Web server Environment 
  - on the next Screen Enter an application name, I named mine: 'SampleApp'
  
This will set up a new environment with ruby selected as your platform. 

Pay close attention to the version and puma version that is required for current AWS Elastic beanstalk deploys. You must have the most up-to-date version of both ruby & puma in your gemfile that is listed on this page: https://docs.aws.amazon.com/elasticbeanstalk/latest/platforms/platforms-supported.html#platforms-supported.ruby

# SSH Setup And Shell Commands
Follow these steps to setup an elastic beanstalk environment connected to an AWS RDS Database 
</br>
Review how to run shell commands such as dropping, creating, migrating or seeding your database 
</br>
  - $ eb init
    - Check that the environment you are wanting to connect to is listed in your .elasticbeanstalk/config.yml file, like so:
    </br>
      branch-defaults: </br>
        aws: </br>
          environment: your-environment-name </br>
        your-branch-name: </br>
          environment: your-environment-name </br>
      environment-defaults: </br>
        your-environment-name: </br>
          branch: null </br>
          repository: null </br>
      global: </br>
        application_name: your-rails-app-name </br>
        default_ec2_keyname: aws-eb </br>
        default_platform: Ruby 2.7 running on 64bit Amazon Linux 2 (or whichever platform you chose when setting up your App on Elasticbeanstalk) </br>
        default_region: us-east-1 (region you choose in app setup) </br>
        include_git_submodules: true </br>
        instance_profile: null </br>
        platform_name: null </br>
        platform_version: null </br>
        profile: eb-cli </br>
        sc: git </br>
        workspace_type: Application
        
        
        </br>
 Rails database.yml file config:</br>
 At the bottom of your file you should have the following configuration to ensure your elasticbeanstalk environment knows where to pass the database_url environment variable from your commmand line </br>
production: </br>
  <<: *default </br>
  adapter: postgresql </br>
  encoding: unicode </br>
  database: <%= ENV['RDS_DB_NAME'] %> </br>
  username: <%= ENV['RDS_USERNAME'] %> </br>
  password: <%= ENV['RDS_PASSWORD'] %> </br>
  host: <%= ENV['RDS_HOSTNAME'] %> </br>
  port: <%= ENV['RDS_PORT'] %> </br> </br>
  
  Rails Example Puma.rb ffile for production: </br> </br>

max_threads_count = ENV.fetch("RAILS_MAX_THREADS") { 5 } </br>
min_threads_count = ENV.fetch("RAILS_MIN_THREADS") { max_threads_count } </br>
threads min_threads_count, max_threads_count </br> </br>

worker_timeout 3600 if ENV.fetch("RAILS_ENV", "development") == "development" </br> </br>

port ENV.fetch("PORT") { 3000 } </br> </br>

environment ENV.fetch("RAILS_ENV") { "development" } </br> </br>

pidfile ENV.fetch("PIDFILE") { "tmp/pids/server.pid" } </br>
bind "unix:///var/run/puma/my_app.sock" </br> 
(Comment out the above two lines while in development)  </br> </br>

pidfile "/var/run/puma/my_app.sock" </br> </br>

plugin :tmp_restart </br> </br>

# AWS CLI commands
</br>

$ eb create your-env-name --envvars RAILS_MASTER_KEY=your-master-key,RAILS_ENV=production,DATABASE_URL=postgresql://your-db-username:your-db-password@your-database-instance-url:port/db-name
</br> </br>
# SSH Instance Commands 
</br>
Cd into current EC2 application instance
</br>
$ cd /var/app/current
</br> </br>
$ RAILS_ENV=production RAILS_MASTER_KEY=your-master-key DATABASE_URL=postgresql://your-db-username:db-password@db-instance-url:db-port/db-name bundle exec rails c
 </br> </br>
 $ RAILS_ENV=production  RAILS_MASTER_KEY=your-master-key DATABASE_URL=postgresql://your-db-username:db-password@db-instance-url:db-port/db-name DISABLE_DATABASE_ENVIRONMENT_CHECK=1 bundle exec rails db:{drop,create,migrate,seed} </br> </br>


# Rails 7 AWS Elastic Beanstalk Deploy Guidelines

## Step 1
- Check current ruby platforms that AWS is accepting for new builds

## Step 2
- Set your rbenv or rvm to use the most current version of ruby that AWS is accepting, currently this is 3.0.4 
run `rbenv local 3.0.4`
