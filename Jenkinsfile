job('TASK-6 JOB-2') {
  description("Job-2 task-6")
  triggers {
    upstream('TASK-6 JOB-1','SUCCESS')
  }
  steps {
  shell('''
if ls /home/divyansh747/task3 | grep html
then
  echo "HTML image launcing..."
  kubectl apply -f /home/divyansh747/task-3-data/html-rs1.yml --kubeconfig /home/divyansh747/.kube/config
  echo "exposing pod HTML"
  kubectl expose rs myhtml --port 80 --type=NodePort --kubeconfig /home/divyansh747/.kube/config
  # sleep for 100s to wait for container comes in running state
  sleep 100
  elif ls /home/divyansh747/task3 | grep php
then
  echo "PHP image launcing..."
  kubectl apply -f /home/divyansh747/task-3-data/php-rs1.yml --kubeconfig /home/divyansh747/.kube/config
  echo "exposing pod PHP"
  kubectl expose rs myphp --port 80 --type=NodePort --kubeconfig /home/divyansh747/.kube/config
  # sleep for 100s to wait for container comes in running state
  sleep 100
else
  echo "Invalid extension JOB failed!!!"
  exit 1
fi

#show output
kubectl get pods --kubeconfig /home/divyansh747/.kube/config
kubectl get service --kubeconfig /home/divyansh747/.kube/config
''')
}
}

job('TASK-6 JOB-3') {
  description("Job-3 task-6")
  triggers {
    upstream('TASK-6 JOB-2','SUCCESS')
  }
  steps {
  shell('''
if kubectl get pods --kubeconfig /home/divyansh747/.kube/config | grep myhtml
then
  echo "myhtml running....."
  port=$(kubectl get service --kubeconfig /home/divyansh747/.kube/config  | grep myhtml |  grep -Eo "[0-9]{5}")
  ip=192.168.99.100
  status=$(curl -o /dev/null -s -w "%{http_code}" $ip:$port)
  if [[ $status == 200 ]]
  then
    echo "html page deployed successfully"
    exit 0
  else
    exit 1
  fi
elif kubectl get pods --kubeconfig /home/divyansh747/.kube/config | grep myphp  
then
  echo "myphp running....."
  port=$(kubectl get service --kubeconfig /home/divyansh747/.kube/config | grep myphp |  grep -Eo "[0-9]{5}")
  ip=192.168.99.100
  status=$(curl -o /dev/null -s -w "%{http_code}" $ip:$port)
  if [[ $status == 200 ]]
  then
    echo "php page deployed successfully"
    exit 0
  else
    exit 1
  fi
fi

''')
  }
  publishers {
        extendedEmail {
            recipientList('divyansh.1999.dr@gmail.com')
            defaultSubject('Build Failed!!!')
            defaultContent('Something broken')
            contentType('text/html')
             attachBuildLog(attachBuildLog = true)
            triggers {
                beforeBuild()
                stillUnstable  {
                    subject('Subject')
                    content('Body')
                    sendTo {
                        recipientList()
                    }
                }
            }
        }
    }
}

buildPipelineView('TASK6 VIEW') {
    filterBuildQueue()
    filterExecutors()
    title('Task-6 Build Pipeline View')
    displayedBuilds(1)
    selectedJob('TASK-6 JOB-1')
    alwaysAllowManualTrigger()
    showPipelineParameters()
    refreshFrequency(60)
}
