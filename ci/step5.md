## Step 5 — Creating Your Sample Application

```note
Note: All commands and files created from now on will start from the folder do-sample-app you created earlier. This is because you are now creating files specific to the sample application that you are going to deploy to your cluster.
```

The Kubernetes `Deployment` you are going to create will use the Nginx image as a base, and your application will be a simple static HTML page. This is a great start because it allows you to test if your deployment works by serving a simple HTML directly from Nginx. As you will see later on, you can redirect all traffic coming to a local `address:port` to your deployment on your cluster to test if it’s working.

```bash
vim do-sample-app/Dockerfile
```

Write the following on it:

```bash
FROM nginx:1.14

COPY index.html /usr/share/nginx/html/index.html
```

This will tell Docker to build the application container from an nginx image.

Now create a new index.html file and open it:

```html
<!DOCTYPE html>
<title>DigitalOcean</title>
<body>
  Kubernetes Sample Application
</body>
```

This HTML will display a simple message that will let you know if your application is working.

You can test if the image is correct by building and then running it.

First, build the image with the following command, replacing dockerhub-username with your own Docker Hub username. You must specify your username here so when you push it later on to Docker Hub it will just work:

```bash
docker build do-sample-app/ -t $DOCKER_USERNAME/do-kubernetes-sample-app
```

Now run the image. Use the following command, which starts your image and forwards any local traffic on port 8080 to the port 80 inside the image, the port Nginx listens to by default:

```bash
docker run --rm -it -p 8080:80 $DOCKER_USERNAME/do-kubernetes-sample-app
```

The command prompt will stop being interactive while the command is running. Instead you will see the Nginx access logs. If you open localhost:8080 on any browser it should show an HTML page with the content of ~/do-sample-app/index.html. In case you don’t have a browser available, you can open a new terminal window and use the following curl command to fetch the HTML from the webpage:

```
curl localhost:8080
```

You will receive the following output:

```bash
<!DOCTYPE html>
<title>DigitalOcean</title>
<body>
  Kubernetes Sample Application
</body>
```

Stop the container (CTRL + C on the terminal where it’s running), and submit this image to your Docker Hub account. To do this, first log in to Docker Hub:

```bash
docker login
```

Fill in the required information about your Docker Hub account, then push the image with the following command (don’t forget to replace the dockerhub-username with your own):

```bash
docker push $DOCKER_USERNAME/do-kubernetes-sample-app
```

You have now pushed your sample application image to your Docker Hub account. In the next step, you will create a Deployment on your DOKS cluster from this image.
