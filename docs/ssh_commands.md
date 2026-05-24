# sudo usermod -aG docker jenkins 

    does this on a Linux system:

    sudo → run the command with administrator (root) privileges
    
    usermod → modify a user account
    
    -aG docker → append (-a) the user to the group (-G) named docker
    
    jenkins → the user being modified
    
    So overall, it adds the jenkins user to the docker group.

# Why this is needed for Jenkins:

    Jenkins usually runs as a Linux user named jenkins
    Docker commands normally require root access
    Users in the docker group can run Docker commands without using sudo
    
    After running this command, Jenkins jobs/pipelines can do things like:
    
    docker build
    docker run
    docker ps
    
    without permission errors.

# Typical use case:

    Jenkins installed on a VM
    Docker installed on the same VM
    Jenkins pipeline needs to build or run containers
    
    Important:
    After running the command, you usually need to restart Jenkins or log out/in for group membership to take effect.

# Common follow-up commands:

    sudo systemctl restart jenkins
    
    and verify with:
    
    groups jenkins