# F1Tenth (Roboracer) Simulators Installation
## F1Tenth Gym
This F1Tenth simulator utilizes OpenAI Gym and can be found at the repository [here](https://github.com/f1tenth/f1tenth_gym/tree/main). The visualization comes from a custom environment that subclasses gym.Env. The instructions given in this document adapt the installation instructions on the repo that install the simulator in a Docker container.

#### Edit the Dockerfile
Make the following edits in the Dockerfile before building the image:  
- Change ``FROM ubuntu:20.04`` to ``FROM nvidia/cuda:12.6.3-devel-ubuntu20.04``
- Change ``RUN pip3 install –upgrade pip`` to ``RUN pip3 install –upgrade pip==24.0``

After making those edits the Dockerfile should build properly using the build command provided in the README.

#### Change the run command
In order to start the Docker container use the following run command:
```
docker run --runtime=nvidia --gpus all -it -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -v $XAUTHORITY:/root/.Xauthority f1tenth_gym_container
```
The addition of ``-v $XAUTHORITY:/root/.Xauthority`` to the command ensures that the Docker container has permission to show the simulator visualization using X11 forwarding.

The example ``python3 waypoint_follow.py`` should now run successfully.

#### Troubleshooting
When trying to run the example you might see the following error message:
```
ImportError: libgthread-2.0.so.0: cannot open shared object file: No such file or directory
```
This can be solved by running the following command:
```
apt update && apt install -y libglib2.0-0
```

## F1Tenth Gym ROS
This F1Tenth simulator adapts the simulator above so that it operates as an ROS 2 node. It can be found at the repository [here](https://github.com/f1tenth/f1tenth_gym_ros?tab=readme-ov-file). This provides a visualization using rviz2. The instructions given in this document adapt the installation instructions With an NVIDIA gpu.

#### Rocker Install
Rocker was not installed system-wide, so a virtual environment was created and rocker was installed in it.

Once Docker, NVIDIA Container Toolkit, and rocker are installed, the provided build and rocker commands for the Docker container should work successfully.

#### Troubleshooting
When attempting to launch the simulator you may see the following error:
```
Error: Failed to create an OpenGL context 
```
Run the following command both on the host machine and in the Docker container:
```
nvidia-smi
```
If this command yields no error on the host machine but yields ``Failed to initialize NVML: Unknown Error`` within the Docker container then run the following on the host machine:
```
sudo vim /etc/nvidia-container-runtime/config.toml
```
Change ``no-cgroups = true`` to ``no-cgroups = false``.
After that restart Docker using the following command:
```
sudo systemctl restart docker
```
This solution can also be found [here](https://stackoverflow.com/questions/72932940/failed-to-initialize-nvml-unknown-error-in-docker-after-few-hours)





