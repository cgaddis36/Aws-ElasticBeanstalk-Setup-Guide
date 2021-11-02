# Aws-ElasticBeanstalk-Setup-Guide
Cheatsheet for AWS Elastic Beanstalk Rails 6 Setup
- This Guide Presumes that you already have ample knowledge of the basics of working in rails, postgres, git and terminal commands

#Deploy Rails 6 Application to Elastic Beanstalk With RDS PostgresQL Database
  1) Set Up AWS Account, User & AWS cli
  2) Create RDS Database and Configure settings for inbound & outbound traffic
  3) Create Elastic Beanstalk Environment 
  4) Configure Rails Application with correct server dependencies for production and ruby version
  5) Set Up Environment variables on ElasticBeanstalk pointed at RDS instance
  6) Deploy Application using CLI
  7) Setup SSH to connect to RDS instance from your terminal to migrate and seed database using your new eb instance
  
Deploying a rails app to AWS can be tricky, especially if this is your first time. To start, you are going to need to ensure that your local ruby version is up to date with the most current version of the platform that AWS offers on the Elastic Beanstalk console. This is an important step that can be easy to mess up, so follow closely. 
  From your terminal, in the root of your app directory, run:
  $ ruby -v
  this will check the Ruby version you are running, the output will be similar to: 'ruby 2.7.4p191 (2021-07-07 revision a21a3b7d23) [arm64-darwin20]'
  It is important to note the trailing text after your initial version
  Login to you AWS Console. 
  Select 'Services' from the top left navigation bar 
  Under 'Compute' select 'Elastic Beanstalk'
  - This will take you to the Elastic Beankstalk Console. From here, Select 'Create Application', select Web server Environment 
  on the next Screen Enter an application name
This will set up a new environment with  

#SSH Setup And Shell Commands
Follow these steps to setup an elastic beanstalk environment connected to an AWS RDS Database and run shell commands such as dropping, creating, migrating or seeding your database 
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

