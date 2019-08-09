pipeline {
    // The running agent is node labled as a "master", in this case, it the master. It can also assign to multiple workers or groups.
    agent { label "master"}
    stages {
        // Delete the workspace for duplicate configuration which may cause an error.
        //stage('Delete the workspace'){
            //steps {
              //sh "sudo rm -rf $WORKSPACE/*"
            //}
        //}
        // Install the ChefDK on the new container
        stage ('Installing ChefDK'){
            steps{
                script {
                    // When the chef-client exist, skip it, if not, go ahead and install chefdk
                    def exists = fileExists '/usr/bin/chef-client'
                    if (exists){
                        echo "Skipping ChefDK install - already installed"
                    } else{ 
                        echo "Install ChefDK"
                        sh 'sudo yum install -y wget'
                        sh 'wget https://packages.chef.io/files/stable/chefdk/4.2.0/el/7/chefdk-4.2.0-1.el7.x86_64.rpm'
                        sh 'sudo rpm -Uvh chefdk-4.2.0-1.el7.x86_64.rpm'
                    }
                }
            }
        }
        // Run the Test Kitchen 
        //stage ("Run Test Kitchen"){
          //  steps{
            //    sh 'sudo kitchen test'
            //}
        //}
        stage('Acceptance Testing') {
         steps {
             parallel(
                 Cookstyle: {
                    sh 'chef env –chef-license accept'
                    sh'echo "Starting cookstyle (rubocop): "'
                    sh'cookstyle'
                 },
                FoodCritic: {
                    sh'echo "Starting foodcritic: "'
                    sh'foodcritic . --tags -FC078'
                 },
                ChefSpec: {
                    sh'echo Starting ChefSpec: '
                    sh'chef exec rspec –chef-license accept'
                }
              )
            }
        }
        stage ("Upload Cookbook"){
            steps{
                sh 'echo $JOB_NAME'
                sh 'rm -rf /home/centos/chef-repo/cookbooks/learn_chef_httpd'
                sh 'mkdir /home/centos/chef-repo/cookbooks/learn_chef_httpd'
                sh 'mv $WORKSPACE/* /home/centos/chef-repo/cookbooks/learn_chef_httpd'
                sh 'cd /home/centos/chef-repo/'
                sh 'knife cookbook upload learn_chef_httpd --force -o /home/centos/chef-repo/cookbooks -c /home/centos/chef-repo/.chef/knife.rb'
               
            }
        }
      }
}
