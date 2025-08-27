# Jenkins jnpl
Configuration for creating an image for CI in Jenkins

## How To Run
The configuration requires parameterized variables for the image tag and the versions of Java, Gradle, Python, and Node.



## Open api without tls

```console
sudo dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375
```

## Module
* TODO