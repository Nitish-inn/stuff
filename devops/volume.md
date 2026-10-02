What is a Docker Volume?
A Docker volume is a storage area managed by Docker that allows data to persist even after a container is deleted.
Think of it like a pen drive attached to a container.
             Docker Container
          ┌──────────────────┐
          │                  │
          │  Application     │
          │  /app/data       │
          │       │          │
          └───────┼──────────┘
                  │
                  ↓
          ┌──────────────────┐
          │ Docker Volume    │
          │   mydata         │
          │                  │
          │  important data  │
          └──────────────────┘
Why do we need volumes?
Suppose you run a MySQL container and store database data inside it.
MySQL Container
      ↓
  Database data
If you delete the container:
docker rm mysql
the data inside the container can be lost.
Instead, use a volume:
MySQL Container
      ↓
Docker Volume
      ↓
Database data
Now you can delete and recreate the container, while the volume keeps the data.
Example: Creating a Volume




STEP 1



Step 1 — Create a volume
docker volume create myvolume
Docker creates a volume called:
myvolume
You can check it:
docker volume ls
You might see:
DRIVER    VOLUME NAME
local     myvolume
Step 2 — Use the volume with a container
For example:
docker run -d --name mycontainer -v myvolume:/data nginx
Let's break this command:
docker run
Create a container.
-d
Run it in the background.
--name mycontainer
Give the container the name mycontainer.
-v myvolume:/data
Attach the volume myvolume to /data inside the container.
nginx
The Docker image we are using.
So:
Docker Volume                  Container
┌───────────────┐             ┌───────────────┐
│   myvolume    │────────────→│    /data      │
│               │             │               │
│ stored data   │             │ application   │
└───────────────┘             └───────────────┘
Step 3 — Put a file into the volume
Enter the container:
docker exec -it mycontainer bash
Now inside the container:
echo "Hello Docker" > /data/message.txt
Check it:
cat /data/message.txt
Output:
Hello Docker
The file is stored in the volume.
Step 4 — Delete the container
Exit the container:
exit
Then:
docker rm -f mycontainer
The container is gone, but the volume still exists.
Check:
docker volume ls
You should still see:
myvolume
Step 5 — Create another container using the same volume
docker run -d --name newcontainer -v myvolume:/data nginx
Enter it:
docker exec -it newcontainer bash
Check the file:
cat /data/message.txt
You will get:
Hello Docker
This is the important point:
Even though the old container was deleted, the data was still there because it was stored in the Docker volume.
