# Remove unused Docker containers and images

## Stop all containers

`docker ps -q -a | xargs docker stop`

## Delete all containers

`docker ps -q -a | xargs docker rm`

## Remove all images

Another way of removing all images is:

`docker images -q | xargs docker rmi`

If images have depended children, forced removal is via the -f flag:

`docker images -q | xargs docker rmi -f`
