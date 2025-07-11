pipeline {
  agent any

  triggers {
    pollSCM('* * * * *')  // 🔄 Triggers the pipeline every 1 minute if there's a code change in GitHub
  }

  environment {
    SSH_KEY = '/var/lib/jenkins/.ssh/id_rsa'       // ✅ Jenkins private SSH key path (to connect to Ansible server)
    ANSIBLE_USER = 'root'                          // 👤 Username for Ansible server login
    ANSIBLE_IP = '13.201.52.184'                    // 🌐 Ansible server's public IP
    REMOTE_PATH = '/root/cicdk8'                 // 📁 Folder in Ansible server to copy code into
  }

  stages {

    stage('Clone') {
      steps {
        git 'https://github.com/Sanjeev14-yadav/cicd-scrap-project.git'
      }
    }

    stage('Test Trigger') {
      steps {
        echo '✅ Build triggered by GitHub push or pollSCM'
      }
    }

    stage('Copy to Ansible Server') {
      steps {
        sh '''
          echo "📦 Copying project code to Ansible server..."

          # ✅ Make sure the remote folder exists
          ssh -i $SSH_KEY $ANSIBLE_USER@$ANSIBLE_IP "mkdir -p $REMOTE_PATH"

          # ✅ Copy current project code to Ansible server
          scp -i $SSH_KEY -r . $ANSIBLE_USER@$ANSIBLE_IP:$REMOTE_PATH

          echo "✅ Code successfully copied to Ansible server at $REMOTE_PATH"
        '''
      }
    }
    stage('Ansible Deploy') {
      steps {
       sh '''
        echo "🚀 Running Ansible playbook to build & push Docker images..."
        ssh -i $SSH_KEY $ANSIBLE_USER@$ANSIBLE_IP "ansible-playbook $REMOTE_PATH/ansible/playbook.yml"
      '''
      }
    }

  }
}
