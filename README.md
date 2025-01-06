# **nodejs API minikube express-gateway postman**

目前有部分的銀行建置容器化中台服務，將需要做資料運算先在中台處理完成，再發送電文到台外幣主機，減輕主機的負擔。

模擬情境：
此案例是用Nodejs建立模擬銀行的API查詢交易服務，並將服務部署到minikube容器化中台上，並使用express gateway做為服務的APIM，最後，並且使用postman打API查詢交易，使用AES加密發送上行電文，收到下行電文後用AES解密結果，但中台的服務沒有再串接到後端主機。

可學習到：
1. 銀行API服務建置架構概念觀念
2. 服務部署的操作
3. 服務節點設定
4. API授權管理

# 將Nodejs API部署到minikube

 ##1. 在minikube建立image
 ```
 eval $(minikube docker-env)
 
 docker build -t node-app .
 
 #查詢minikube的images
 minikube ssh
 
 docker@minikube:~$ dokcer images
 ```

![ifxquerytest](https://github.com/astinchen/nodejs-API-minikube-express-gateway-postman/blob/main/pic/dokcer_images.png)
 
 ##2. 將服務部署到minikube容器化平台
 ```
 kubectl apply -f deployment.yaml
  
 kubectl get deployment
 ```

![ifxquerytest](https://github.com/astinchen/nodejs-API-minikube-express-gateway-postman/blob/main/pic/getdeployment.png)

 ```
 kubectl get pods
 ```
![ifxquerytest](https://github.com/astinchen/nodejs-API-minikube-express-gateway-postman/blob/main/pic/getpods.png)

 ##3. 部署服務的service
 ```
 kubectl apply -f deploymentservice.yaml
 ```
 
 ##4. 開啟service直連
 ```
 minikube tunnel
 ```
![ifxquerytest](https://github.com/astinchen/nodejs-API-minikube-express-gateway-postman/blob/main/pic/servicetunnel.png)
 
 ##5. 查看minikube dashboard

![ifxquerytest](https://github.com/astinchen/nodejs-API-minikube-express-gateway-postman/blob/main/pic/dashboardpods.png)
![ifxquerytest](https://github.com/astinchen/nodejs-API-minikube-express-gateway-postman/blob/main/pic/dashboardservice.png)


# 使用Express Gateway建置服務authorization及routing
 ##1. 安裝Express Gateway
 ```
 sudo npm install -g express-gateway
 ```
 
 ##2. 建立Express Gateway
 ``` 
  eg gateway create
 
 ➜ eg gateway create
 ? What is the name of your Express Gateway? nodeapp-gateway
 ? Where would you like to install your Express Gateway? nodeapp-gateway
 ? What type of Express Gateway do you want to create? (Use arrow keys)
 ❯ Getting Started with Express Gateway
   Basic (default pipeline with proxy)
 
 ```
 
 ##3. 起動 nodejs
 ```
  cd nodeapp-gateway && npm start
 ```
 
 ##4.  修改gateway.config.yml
 
 cd nodeapp-gateway/config
 
 serviceEndpoints的url修改為連到部署在minikube上的service url
  
 ```
   serviceEndpoints:
   httpbin:
     url: 'http://10.100.44.82:5000'
 ``` 
 
 ##5. 依照官網建立將授權驗證機制
 
 https://www.express-gateway.io/getting-started/ 

# 使用Postman打查詢API，驗證授權
##1. 將env參數及collection匯入postman

##2. 沒有授權發送電文無法查詢

![ifxquerytest](https://github.com/astinchen/nodejs-API-minikube-express-gateway-postman/blob/main/pic/noAuthorization.png)

##3. 起用授權發送交易收到查詢結果

![ifxquerytest](https://github.com/astinchen/nodejs-API-minikube-express-gateway-postman/blob/main/pic/Authorization.png)

# 後續研究
##1. 建立ServiceMonitor監控nodeapp的資源變化

##2. 建立服務的API通用線上規格文件swagger

