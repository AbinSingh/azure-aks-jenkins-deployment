# Step 1 — Remove Old Jenkins Config Again
    Purpose
    
    Clean up old invalid Jenkins key/repository configuration.
    
    Run:
    
    sudo rm -f /etc/apt/sources.list.d/jenkins.list
    sudo rm -f /etc/apt/keyrings/jenkins.gpg

# Step 2 — Download NEW Jenkins Key
    Purpose
    
    Import the updated Jenkins repository signing key.
    
    Run EXACTLY:
    
    curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key \
    | sudo tee /etc/apt/keyrings/jenkins.asc > /dev/null

# Step 3 — Add Jenkins Repository Again
    Purpose
    
    Register Jenkins package source using the updated signing key.
    
    Run:
    
    echo "deb [signed-by=/etc/apt/keyrings/jenkins.asc] https://pkg.jenkins.io/debian-stable binary/" \
    | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Step 4 — Update Package List
    Purpose
    
    Refresh repositories and verify Jenkins repository signature.
    
    Run:
    
    sudo apt update

# Step 5 — Install Jenkins
    Purpose
    
    Install Jenkins successfully from the now-trusted repository.
    
    Run:
    
    sudo apt install jenkins -y

# Step 6 — Verify Jenkins Running
    Purpose
    
    Ensure Jenkins service started correctly.
    
    Run:
    
    sudo systemctl status jenkins
    
    You should see:
    
    active (running)

# Step 7 — Check Jenkins Error Logs
    Purpose
    
    See the exact reason Jenkins failed.
    
    Run:
    
    sudo journalctl -u jenkins --no-pager -n 50
    
    Copy the last 10–20 lines if possible.
    
    But before that, try the next fix because I strongly suspect it’s Java path related.
    
    # Step 8 — Install Java 21
    Purpose
    
    Install the newer Java version required by Jenkins.
    
    Run:
    
    sudo apt install openjdk-21-jdk -y

# Step 8 — Find Java Path
    Purpose
    
    Jenkins sometimes cannot automatically locate Java.
    
    Run:
    
    which java
    
    Example output:
    
    /usr/bin/java
    
    Now run:
    
    readlink -f /usr/bin/java
    
    Example output:
    
    /usr/lib/jvm/java-17-openjdk-amd64/bin/java
    
    Important part:
    
    /usr/lib/jvm/java-17-openjdk-amd64
    
    That is your JAVA_HOME.

# Step 9 — Configure Jenkins Java Path
    Purpose
    
    Explicitly tell Jenkins where Java is installed.
    
    Edit Jenkins config:
    
    sudo nano /etc/default/jenkins
    
    Look for:
    
    #JAVA_HOME=
    
    OR add this line:
    
    JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
    
    Save file:
    
    CTRL + O
    Enter
    CTRL + X

# Step 10 — Restart Jenkins
    Purpose
    
    Apply Java configuration and restart Jenkins service.
    
    Run:
    
        sudo systemctl restart jenkins
    
    Then check:
    
    sudo systemctl status jenkins