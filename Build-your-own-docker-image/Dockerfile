# Use official python image as the Base image to extend
FROM python:3.14.0a7-alpine3.21   
	
# Set the working directory
WORKDIR /app    

# Copy the contents from current working directory on the host machine to the image
COPY . /app    

# Run command to install python package
RUN python -m pip install -r requirements.txt    


# Our flask app will run on port 5000 hence it is important to open that port to allow incoming connections.
EXPOSE 5000     

# Default command to run on container startup
CMD ["python","-u","app.py"]  

