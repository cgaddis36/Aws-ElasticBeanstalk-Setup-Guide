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
Go ahead and create an AWS account if you have not already.
</br>
Click on the Services Navigation tab at the top and search for IAM. Select this Service
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
On the Next Page, it will ask you to add tags.
<br/>
Go ahead and add a tag so you can recognize it later, Name:your-username
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
  
This will set up a new environment with  

#SSH Setup And Shell Commands
Follow these steps to setup an elastic beanstalk environment connected to an AWS RDS Database 
</br>
Review how to run shell commands such as dropping, creating, migrating or seeding your database 
  - $ eb init 
    - Check that the environment you are wanting to connect to is listed in your .elasticbeanstalk/config.yml file, like so:
      branch-defaults:
        aws:
          environment: your-environment-name
        your-branch-name:
          environment: your-environment-name
      environment-defaults:
        your-environment-name:
          branch: null
          repository: null
      global:
        application_name: your-rails-app-name
        default_ec2_keyname: aws-eb
        default_platform: Ruby 2.7 running on 64bit Amazon Linux 2 (or whichever platform you chose when setting up your App on Elasticbeanstalk)
        default_region: us-east-1 (region you choose in app setup)
        include_git_submodules: true
        instance_profile: null
        platform_name: null
        platform_version: null
        profile: eb-cli
        sc: git
        workspace_type: Application

