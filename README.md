GitLab CI - Chrome headless
===========================

This project allows you to work with GitLab CI and use a container that can both 
git push to an origin and allow you to run a headless chrome.

Although it can be used with different CI tools,
it was originally created and tested with GitLab CI only.

Build is tested with: `Google Chrome 65.0.3325.146`

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

Please note that `$USER`, `$REMOTE` and `$PROJECT` are variables you need to set in the
CI/CD environment variables section. So just don't copy paste this example :-)

SSH Tunnel
----------
A tunnel is needed for behat (which is running on a remote server) to access
Chrome (which is running in this container).

The -R flag specifies that connections to the given TCP port or Unix socket on 
the remote (server) host are to be forwarded to the local side.
Read all info on man ssh for the -R flag.
