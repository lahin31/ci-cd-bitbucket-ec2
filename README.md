# Create a CI/CD Pipeline with BitBucket and EC2 Instance

- Navigate to your EC2 Instance, make sure you have Elastic IP.
- Create a folder and inside of this clone your repo.
  - If you do clone this with SSH Link, you can generate you ssh key by typing `ssh-keygen -t ed25519 -C "{email}"`
  - Type `cat /home/ubuntu/.ssh/id_ed*****.pub`, copy that key
  - Go to BitBucket's Personal Settings and SSH Keys and create a new SSH key that key you just copied.
  - Clone the Repo again.
- Inside the Repository's Pipeline Section go to SSH keys and put the Instance's PEM key to Private Key Field and for Public Key Field you can get the key just type `ssh-keygen -y -f <Path to .pem file with file>`
- Go to your intance and inside /home/ubuntu create a file called deploy.sh
- Put this inside deploy.sh file (you can add more based on your need)

```
cd ~/{path of our folder} && git pull origin master
cd ~/{path of our folder} && npm install
```

- Inside bitbucket-pipelines.yml put

```
image: node:16

pipelines:
  branches:
    master:
      - step:
          name: Install dependencies
          script:
           - npm install
      - step:
          name: Deploy to EC2
          script:
            - echo "Deploying to production environment"
            - pipe: atlassian/ssh-run:0.2.2
              variables:
                SSH_USER: 'ubuntu'
                SERVER: '107.20.138.3'
                COMMAND: '/home/ubuntu/deploy.sh'
```
