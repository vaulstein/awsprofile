# AWS PROFILE

## INTRODUCTION

 Managing multiple AWS accounts from `aws-cli` tends to be a little tedious. 
 When you need to switch between multiple AWS accounts, it seems to be a hassle keeping a
 track of the current account, all the available account names. 
 
 Even setting the variable and using it to switch between accounts doesn't seem like 
 the right deal.
 
    export AWS_PROFILE=user2
 

 Aliases can be used to manage AWS profiles in a better way.
 
 With aliases we can do the following:
 
 * List available profiles
 * View current profile configuration
 * Switch to another profile
 
## Creating Aliases

  First we will create functions that would be invoked by our Alias
  
  Create a file as below:
  
     vim ~/.awsAliases
  
  Next add the below lines in the file:
    
    #!/bin/bash
    function _awsListAll() {
 
        credentialFileLocation=${AWS_SHARED_CREDENTIALS_FILE};
        if [ -z $credentialFileLocation ]; then
            credentialFileLocation=~/.aws/credentials
        fi
     
        while read line; do
            if [[ $line == "["* ]]; then echo "$line"; fi;
        done < $credentialFileLocation;
    };
     
    function _awsSwitchProfile() {
       if [ -z $1 ]; then  echo "Usage: awsp profilename"; return; fi
       
       exists="$(aws configure get aws_access_key_id --profile $1)"
       if [[ -n $exists ]]; then
           export AWS_DEFAULT_PROFILE=$1;
           export AWS_PROFILE=$1;
           export AWS_REGION=$(aws configure get region --profile $1);
           echo "Switched to AWS Profile: $1";
           aws configure list
       fi
    };
  
  Now if you are using *bash*, open bash_profile as below:
  
    vim ~/.bash_profile
    
  And add the following files:
  
    . ~/.awsAliases
    alias awsall="_awsListAll"
    alias awsp="_awsSwitchProfile"
    alias awswho="aws configure list"
    
  If you are using zshrc, then add the lines in *~/.zshrc*
  
  Now, reload your bash profile or zshrc as below
  
    source ~/.bash_profile # For zshrc source ~/.zshrc
   
## Running Aliases
  
  To list all the profiles use:
  
    awsall
    
  To switch between profiles use:
  
    awsp <profileName>
   
  To show current profile details:
  
    awswho
    
## Credits

Code based on article written by [Carl Nordenflet](https://twitter.com/carl_nordenfelt)