# prometheus-grafana
    1  kubectl create namespace monitoring
    4  kubectl get namespace
    5  clear
    6  git clone https://github.com/bibinwilson/kubernetes-prometheus
    7  cd kubernetes-prometheus/
    8  clear
    9  ls
    10  kubectl create -f config-map.yaml -n monitoring
    11  kubectl create -f clusterRole.yaml -n monitoring
    12  kubectl create -f prometheus-deployment.yaml -n monitoring
    13  clear
    14  kubectl get pods -n monitoring
    15  kubectl get pods -n monitoring -w
    16  clear
    17  kubectl create -f prometheus-service.yaml -n monitoring
    18  kubectl get svc -n monitoring
        
        
        
        NOTE(if you are using katakoda the goto to right sides + button and got to web console and paste the  port given by command number 18 for examole:32000 and yoy will redirect to web console);

    19) create configMap.yml and paste the below code and run the below command :
    kubectl create -f configMap.yml -n monitoring
        
apiVersion: v1
kind: ConfigMap
metadata:
  name: grafana-datasources
  namespace: monitoring
data:
  prometheus.yaml: |-
    {
        "apiVersion": 1,
        "datasources": [
            {
               "access":"proxy",
                "editable": true,
                "name": "prometheus",
                "orgId": 1,
                "type": "prometheus",
                "url": "http://prometheus-service.monitoring.svc:8080",
                "version": 1
            }
        ]
  }
    
    
    20) create Deployment.yml and paste the below code and run command
     kubectl create -f deployment.yml -n monitoring 
        after check whether the pod is created or not using kubectl get pods -n monitoring
    
    
   apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      name: grafana
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana:latest
        ports:
        - name: grafana
          containerPort: 3000
        resources:
          limits:
            memory: "2Gi"
            cpu: "1000m"
          requests: 
            memory: "1Gi"
            cpu: "500m"
        volumeMounts:
          - mountPath: /var/lib/grafana
            name: grafana-storage
          - mountPath: /etc/grafana/provisioning/datasources
            name: grafana-datasources
            readOnly: false
      volumes:
        - name: grafana-storage
          emptyDir: {}
        - name: grafana-datasources
          configMap:
              defaultMode: 420
              name: grafana-datasources
                
    21) create service.yml and paste the below code and run the following command 
                 kubectl create -f service.yml -n monitoring and check the service using
                     kubectl get svc -n monitoring  
                     goto side + button and type the port given for example:32000

apiVersion: v1
kind: Service
metadata:
  name: grafana
  namespace: monitoring
  annotations:
      prometheus.io/scrape: 'true'
      prometheus.io/port:   '3000'
spec:
  selector: 
    app: grafana
  type: NodePort  
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 32000

         
22) this link gives the templates required to grafana copy the  port from this link and paste it to the browser 
          
https://grafana.com/grafana/dashboards?search=kubernetes

port link for analysis of pod
https://grafana.com/grafana/dashboards/6879

    
23) take the port and goto to grafanna web console and on left side + click on import and paste the port there and select promothesis and now you can see the analysis of pods and all other process
