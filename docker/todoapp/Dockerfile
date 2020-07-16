#define base images
FROM node 
LABEL maintainer iamsnowball3@gmail.com
RUN git clone -q https://github.com/docker-in-practice/todo.git
#move to the new cloned directory
WORKDIR todo
RUN npm install > /dev/null
EXPOSE 8000
#specifies which commands will be run on startup
CMD ["npm", "start"] 
