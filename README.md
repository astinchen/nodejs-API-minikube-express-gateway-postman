# nodejs-API-minikube-express-gateway-postman
此案例是用Nodejs建立模擬銀行的API查詢交易服務，並將服務部署到minikube容器化中台上，並使用express gateway做為服務的APIM，最後，並且使用postman打API查詢交易，使用AES加密發送上行電文，收到下行電文後用AES解密結果，但中台的服務沒有再串接到後端主機。