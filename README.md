<h1>Atividades CompassUOL</h1>
<h2>Descrição Geral:</h2>

Este repositório apresenta exercícios práticos sobre Kubernetes, abordando conceitos como Pods, Deployments, Jobs, PersistentVolume, entre outros. Os exercícios serão realizados em um ambiente Windows 11, utilizando Minikube. Para que tudo funcione corretamente, é necessário instalar o `kubectl` separadamente.  

O objetivo deste projeto é consolidar conhecimentos sobre Kubernetes, explorando suas funcionalidades básicas por meio de atividades práticas.

<h2>Tecnologias Utilizadas:</h2>

- Kubernetes;

> [!IMPORTANT]  
> Este repositório **não inclui instruções** para a instalação do Kubernetes, Kubectl ou Minikube. No entanto, você pode seguir as documentações oficiais:  
> - [Documentação oficial do Kubernetes](https://kubernetes.io/docs/home/)  
> - [Instalação do Minikube](https://minikube.sigs.k8s.io/docs/start/)  
<h2></h2>

<h2>Lista das Atividades:</h2>

1. [Crie um pod](#1-criar-um-pod) chamado **"my-pod"** usando uma imagem simples como **"nginx"** e verifique seu estado com os comandos de monitoramento do Kubernetes.  
2. [Implante um Deployment](#2-implantando-um-deployment) chamado **"my-deployment"** com três réplicas de uma aplicação baseada na imagem **"httpd"**. Atualize a imagem do Deployment para uma versão mais recente.  
3. [Crie um ConfigMap](#3-criar-um-configmap) chamado **"app-config"** com uma variável de configuração personalizada. Monte o ConfigMap em um pod e verifique se o valor foi aplicado corretamente.  
4. [Crie um Secret](#4-criar-um-secret) chamado **"app-secret"** contendo informações sensíveis. Injete o Secret como uma variável de ambiente em um pod e teste se está acessível.  
5. [Configure um PersistentVolume](#5-configurando-persistent-volume) de **1Gi** de armazenamento local e vincule-o a um PersistentVolumeClaim. Monte o volume em um pod e salve arquivos para verificar a persistência.  
6. [Crie um serviço do tipo **ClusterIP**](#6-criar-um-clusterip) para um Deployment chamado **"backend"** e teste a conectividade interna entre pods usando o nome do serviço.  
7. [Implante um Job](#7-implantando-um-job) chamado **"batch-job"** que execute um comando simples e termine. Verifique os logs do Job para confirmar sua execução.  
8. [Crie um **Horizontal Pod Autoscaler**](#8-criar-um-horizontal-pod-autoscaler) para um Deployment chamado **"hpa-deployment"** e configure-o para escalar com base no uso de CPU. Aumente a carga e observe o escalonamento.  
9. [Crie um serviço do tipo **NodePort**](#9-criar-um-nodeport) para expor externamente um Deployment chamado **"webapp"**. Acesse o serviço usando o endereço IP do Minikube e a porta atribuída.  
10. [Crie um pod chamado **"restart-pod"**](#10-pod-com-politica-de-restart) com a política de reinício configurada como **"OnFailure"**. Provoque uma falha no pod e observe seu comportamento.

<h2>Materias de Apoio:</h2>

- [Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
- [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)
- [Secrets](https://kubernetes.io/pt-br/docs/concepts/configuration/secret/)
- [Persistent Volume](https://kubernetes.io/pt-br/docs/concepts/storage/persistent-volumes/)
- [Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/)
- [Horizontal Pod Autoscaling](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)

<h2></h2>

<h3>1. Criar um Pod:</h3>

1.1 Comando para criar um Pod com a imagem do nginx:

```
kubectl run my-pod --image=nginx:alpine
```
Saída esperada: `pod/my-pod created`

1.2 Para verificar os status agora:

```
kubectl get pod
```
- Exemplo:
![image](https://github.com/user-attachments/assets/88e33ad8-a424-4304-b80d-cf7dc908bebe)

```
kubectl logs pod/my-pod
```
- Exemplo:
![image](https://github.com/user-attachments/assets/3605335d-8f53-4855-8b08-1a7c32f934ad)

```
kubectl describe pod/my-pod
```
- Exemplo:
![image](https://github.com/user-attachments/assets/b601b0da-aa2c-4c0d-adb8-969531dc76de)

<h3>2. Implantando um Deployment:</h3>

2.1 Comando para criar o Deployment:

```
kubectl create deployment my-deployment --image=httpd:2.4
```
Saída esperada: `deployment.apps/my-deployment created`

2.2 Aplicar as três replicas:
```
kubectl scale deployment/my-deployment --replicas=3
```
Saída esperada: `deployment.apps/my-deployment scaled`

2.3 Atualizar a imagem do Deployment:
```
kubectl set image deployment my-deployment httpd=httpd:latest
```
Saída esperada: `deployment.apps/my-deployment image updated`

2.4 Para visualizar se tudo deu certo basta utilizar os comandos a baixo:
```
kubectl get deployment my-deployment
kubectl describe deployment/my-deployment
```

<h3>3. Criar um ConfigMap:</h3>

3.1 Comandos para criar:

```
kubectl create configmap app-config --from-literal=APP_NAME=MeuApp
```
Saída esperada: `configmap/app-config created`

3.2 Crie um arquivo yaml com o seguinte nome **pod-with-configmap**, deve ficar assim `pod-with-configmap.yaml` e coloque o conteudo que está logo abaixo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-configmap
spec:
  containers:
  - name: my-container
    image: nginx
    env:
    - name: APP_NAME
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_NAME
```
Crie o pod com:
```
kubectl apply -f pod-with-configmap.yaml
```

3.3 Para verificar se o valor foi aplicado, entre no container:
```
kubectl exec -it pod-with-configmap -- env
```
Deve aparecer algo assim: `APP_NAME=MeuApp`
- Exemplo:
![image](https://github.com/user-attachments/assets/61ca0bd9-591e-4989-a63c-23f7a18aaf1c)

<h3>4. Criar um Secret:</h3>

4.1 Comandos para criar:
```
kubectl create secret generic app-secret --from-literal=USERNAME=admin --from-literal=PASSWORD=123456
```
Saída esperada: `secret/app-secret created`

4.2 Crie um arquivo yaml com o seguinte nome **pod-with-secret**, deve ficar assim `pod-with-secret.yaml` e coloque o conteudo que está logo abaixo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-secret
spec:
  containers:
  - name: my-container
    image: nginx
    env:
    - name: USERNAME
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: USERNAME
    - name: PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: PASSWORD
```

Criar o pod com:
```
kubectl apply -f pod-with-secret.yaml
```

4.3 Para verificar se o valor foi aplicado, entre no container:
```
kubectl exec -it pod-with-secret -- env
```

Procure por:
`
USERNAME=admin
PASSWORD=supersecret
`

- Exemplo:
![image](https://github.com/user-attachments/assets/fd9f3c94-6284-4775-ba96-439a9ad3e06d)

<h3>5. Configurando Persistent Volume:</h3>

5.1 Criar o PersistentVolume (PV), para isso crie um arquivo yaml com o seguinte nome **persistentVolume**, deve ficar assim `persistentVolume.yaml` e coloque o conteudo que está logo abaixo:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/mnt/data"
```

Comando para aplicar:
```
kubectl apply -f persistentVolume.yaml
```

5.2 Criar o PersistentVolumeClaim (PVC), para isso crie um arquivo yaml com o seguinte nome **persistentVolumeClaim**, deve ficar assim `persistentVolumeClaim.yaml` e coloque o conteudo que está logo abaixo:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Comando para aplicar:
```
kubectl apply -f persistentVolumeClaim.yaml
```

5.3 Para testar se está funcionando é necessario subir um novo Pod, inserir um arquivo, deletar o pod e recriar para conferir se é mantido o arquivo. Vamos fazer isso, crie um arquivo yaml com o nome `pod-with-pvc.yaml` e insira o conteudo abaixo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-pvc
spec:
  containers:
  - name: my-container
    image: nginx
    volumeMounts:
    - name: storage
      mountPath: "/usr/share/nginx/html"
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: my-pvc
```

Comando para criar o pod:
```
kubectl apply -f pod-with-pvc.yaml
```

5.4 Entre no pod:
```
kubectl exec -it pod-with-pvc -- /bin/sh
```

Navegue até o caminho montado:
```
cd /usr/share/nginx/html
echo "Hello, PersistentVolume!" > index.html
```

Saia do pod e exclua-o:
```
exit
kubectl delete pod pod-with-pvc
```

Recrie o pod e confira se o arquivo ainda está lá:
```
kubectl apply -f pod-with-pvc.yaml
kubectl exec -it pod-with-pvc -- cat /usr/share/nginx/html/index.html
```
Saída esperada: `Hello, PersistentVolume!`

<h3>6. Criar um ClusterIP:</h3>

6.1 Criar um arquivo yaml com o nome backend, fica assim `backend.yaml` e coloque o codigo abaixo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: httpd
        ports:
        - containerPort: 80
```

Comando para aplicar:
```
kubectl apply -f backend.yaml
```

6.2  Criar um arquivo yaml com o nome clusterip-service, fica assim `clusterIP.yaml` e coloque o codigo abaixo:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Comando para aplicar:
```
kubectl apply -f clusterIP.yaml
```

6.3 Testar a conectividade interna: Crie um pod temporário para testar:
```
kubectl run test-pod --image=busybox --restart=Never --rm -it -- /bin/sh
```

Dentro do pod, use o nome do serviço para acessar o backend:
```
wget -qO- backend-service
```

- Exemplo:
![image](https://github.com/user-attachments/assets/60b11bc5-3aa9-4e0b-84ef-e505cde7c42f)

<h3>7. Implantando um Job:</h3>

7.1 Criar um arquivo yaml chamado `batch-job.yaml` e coloque o conteudo abaixo:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: batch-job
spec:
  template:
    spec:
      containers:
      - name: job-container
        image: busybox
        command: ["sh", "-c", "echo 'Hello, Kubernetes Job!' && sleep 5"]
      restartPolicy: Never
  backoffLimit: 4
```

7.2 Criar o Job:
```
kubectl apply -f batch-job.yaml
```

7.3 Monitorar o progresso do Job:

Para verificar o estado do Job:
```
kubectl get jobs
```

Para ver os pods criados pelo Job:
```
kubectl get pods
```

7.4 Verificar os logs do Job:

O Job criará um pod. Pegue o nome do pod:
```
kubectl get pods --selector=job-name=batch-job
```

Exemplo de saída:
```
NAME              READY   STATUS      RESTARTS   AGE
batch-job-xxxxx   0/1     Completed   0          10s
```

Substitua `batch-job-xxxxx` pelo nome do pod e veja os logs:
```
kubectl logs batch-job-xxxxx
```
Saída esperada: `Hello, Kubernetes Job!`

<h3>8. Criar um Horizontal Pod Autoscaler:</h3>

8.1 Criar um Deployment:
```
kubectl create deployment hpa-deployment --image=nginx
```

8.2 Criar o HPA:
```
kubectl autoscale deployment hpa-deployment --cpu-percent=85 --min=2 --max=5
```
<b>OBS:</b> Isso cria um HPA que escala entre 2 e 5 réplicas se o uso de CPU ultrapassar 85%.

8.3 Simular carga:
```
kubectl run load-generator --image=busybox -- sh -c "while true; do wget -q -O- http://hpa-deployment; done"
```

Observe o HPA escalando:
```
kubectl get hpa
kubectl get pods
```

<h3>9. Criar um NodePort:</h3>

8.1 Criar um Deployment:
```
kubectl create deployment webapp --image=nginx
```

8.2 Expondo a porta:
```
kubectl expose deployment webapp --type=NodePort --port=80
```

8.3 Acessar via: Minikube IP + NodePort:

Obtendo o NodePort:
```
kubectl get svc
```
- Exemplo:
![image](https://github.com/user-attachments/assets/244fa521-34ff-4e92-8f38-c6213b14ae1d)

Obtendo o Minikube IP:
```
minikube ip
```
- Exemplo:
![image](https://github.com/user-attachments/assets/fb56b220-0590-426a-b697-8fbda770d24e)

Basta fazer a substituição agora:
```
minikube service webapp <Minikube IP>:<NodePort>
```
- Exemplo:
![image](https://github.com/user-attachments/assets/3fdbb8b9-53dd-4f8a-9f54-ca633c79d4a0)

Será aberto automaticamente no seu navegador padrão, algo como isso:

![image](https://github.com/user-attachments/assets/74f523f4-9d3a-4b72-966d-d0d17cd97073)

<h3>10. Pod com politica de restart:</h3>

10.1 Crie um arquivo yaml com o nome restart-pod e inclua o codigo abaixo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: restart-pod
spec:
  restartPolicy: OnFailure
  containers:
  - name: busybox
    image: busybox
    command: ["false"]
```

Comando para aplicar:
```
kubectl apply -f restart-pod.yaml
```

10.2 Verifique o estado do pod. O pod será reiniciado automaticamente, já que o comando falha. Veja os eventos:
```
kubectl describe pod restart-pod
```

<h1>Parabéns você conseguiu concluir todas as atividades!!! 🎉</h1>
