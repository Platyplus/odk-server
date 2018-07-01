# Create the infrastructure in Digital Ocean
## Get list of Digital Ocean regions
curl -X GET -H "Content-Type: application/json" -H "Authorization: Bearer $DOTOKEN" "https://api.digitalocean.com/v2/regions"

## Create droplet in Amsterdam region 3
export DROPLET_NAME=platyplus-1
export DO_REGION=ams3
docker-machine create --driver digitalocean --digitalocean-image ubuntu-16-04-x64 --digitalocean-access-token $DOTOKEN --digitalocean-region $DO_REGION $DROPLET_NAME

## Assign floating IP
From the DO dashboard

## Init Docker swarm in the droplet
### Get IP adress of the droplet
docker-machine ls
### Connect to the droplet
docker-machine ssh $DROPLET_NAME
### then in the droplet
docker swarm init --advertise-addr <floating_ip_address>

## Map floating IP in NameCheap

# Add droplets
See https://www.digitalocean.com/community/tutorials/how-to-create-a-cluster-of-docker-containers-with-docker-swarm-and-digitalocean-on-ubuntu-16-04

# Install ODK
## TODO Let's encrypt
## Connect to the leader droplet
docker-machine ssh $DROPLET_NAME
## Get the docker configuration in the droplet
git clone https://github.com/Platyplus/odk-server
## Build the docker files
cd odk-server
docker build -t odk/db-bootstrap db-bootstrap
docker build -t odk/openldap openldap
docker build -t odk/phpldapadmin phpldapadmin
## Run the stack
docker stack deploy -c docker-compose.yml odkstack

# Create LDAP users and groupd
## Create an SSH tunnel
docker-machine ssh $DROPLET_NAME -L 0.0.0.0:4000:localhost:40000
## Connect to PHP LDAP Admin on your local machine
Browse to https://localhost:40000/
Default admin is cn=admin,dc=platyplus,dc=io, password admin
See https://docs.opendatakit.org/odk2/sync-endpoint/#ldap
# Update ODK
## TODO




1. connect from your local machine to the docker host machine:

docker-machine ssh odk-platy-plus-docker1 -L 0.0.0.0:40000:localhost:40000

2. then browse https://127.0.0.1:40000 from your local machine




docker run --rm -p 443:443 -p 80:80 --name letsencrypt -v "/root/keys/letsencrypt:/root/keys/letsencrypt" certbot/certbot certonly -n -m "plmercereau@gmail.com" -d odk.platy.plus --standalone --agree-tos


docker run --rm --name letsencrypt -v "/root/keys/letsencrypt:/root/keys/letsencrypt" \
    certbot/certbot:latest \
    renew --quiet
