GitLab CI - Chrome headless
===========================

This project allows you to work with GitLab CI and use a container that can both 
git push to an origin and allow you to run a headless chrome.

This is useful when you have another external git repository hosting your codebase of
your application and at the same time you are in need of headless browser capabilities
for purposes such as Behat testing from that remote server.


Example implementation .gitlab-ci.yml file:

    image: novitsh/gitlab-ci-chrome-headless
    
    stages:
      - develop
    
    before_script:
      - echo "Establishing SSH tunnel in the background."
      - ssh -R 9222:localhost:9222 $USER@$REMOTE -fN
    
    deployment to develop:
      stage: develop
      script:
        - echo "Now pushing new code to remote git repository."
        - git-push $USER@$REMOTE:$PROJECT.git develop
        - ssh $USER@$REMOTE "cd /project/behat && bin/behat --config behat.yml"
        
Breakdown
---------
Above example code will basically open an SSH tunnel, push the Gitlab repo to a 
remote server, and then connect to it to run some Behat tests.