# globus_compute
Investigating the use of Globus Compute as a remote computing solution

To run this tutorial you'll need a Globus account that you can log into through any of the provided methods, and you'll need to install conda and Docker on your local system.

# Compute endpoint running in Docker

The example sets up a Globus Compute Endpoint that runs in a Docker container on your local system. The Endpoint is configured in "single-user" mode, meaning that only one Globus account is associated with all of the tasks that get run by the endpoint. In the example, this will be your Globus account.

First, build the Docker image with:

    cd globus_compute
    docker build -t globus_endpoint -f endpoint_dockerfile .

Then start a container running from the image with:

    docker run -it --rm globus_endpoint

This will give you a terminal running as the root user inside the container's Debian Linux system. As that root user, start the endpoint running with:

    globus-compute-endpoint start example_endpoint

That command will output a URL that you need to visit to log on with your Globus account. Following that procedure will eventually take you to a webpage with a "Native App Authorization Code" displayed on it. Copy and paste that code back into the Docker container's terminal to start the Endpoint running as a daemon in the Container.

When the daemon is started, it will print out a message saying "`Starting endpoint; registered ID: [some_id_string]`". You should copy that ID string and paste it into the second cell in the `example_notebook` to submit tasks to the Endpoint running in the Docker container. Keep the window with the Docker container's terminal open; when you "exit" the container it will delete itself (and the Endpoint along with it).

# Example of interacting with the endpoint

On your local system, create a new conda environment with python 3.10 installed and then activate it:

    conda create -n globus_compute python=3.10
    conda activate globus_compute

Pip install the globus compute SDK and other dependencies for running the example notebook:

    cd globus_compute
    pip install -r requirements.txt

You should be able to run the examples in the notebook once you've copied the Endpoint ID from the Docker container's terminal window into the second cell as the "`example_endpoint_id`" variable value.

# Compute endpoint on Rockfish

You can set up the single-user compute endpoint running on Rockfish in a similar way to running it in a Docker container, but with a couple extra steps. First, assuming that you have a Globus Service Account set up to run the endpoint and authenticate to it remotely, set the `GLOBUS_COMPUTE_CLIENT_ID` and `GLOBUS_COMPUTE_CLIENT_SECRET` environment variables on your Rockfish login node. Then clone this repository, create and activate a new conda environment, and install the dependencies from the requirements file as described above. Then initialize an endpoint with:

    globus-compute-endpoint configure endpoint_name

Take note of the configuration file location that running that command prints out, and copy the contents of the [rockfish_config.yaml](./rockfish_config.yaml) file in this repository into that configuration file. The configurations in that file show an example of how to set up the endpoint to submit any tasks to the slurm queue on Rockfish. Then you can start the endpoint with:

    globus-compute-endpoint start endpoint_name

and submit jobs to it remotely as you would expect with its endpoint ID. You will need to set the `GLOBUS_COMPUTE_CLIENT_ID` and `GLOBUS_COMPUTE_CLIENT_SECRET` environment variables on the remote systems that will submit jobs to the endpoint, and also set them on Rockfish whenever you go to start the endpoint again.
