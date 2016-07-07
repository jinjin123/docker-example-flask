# docker-example-flask


    script example:

        [jenkins@test_machine docker-example-flask]$ cat /tmp/test.sh    
        #!/bin/bash
        #
        #2016/7/7

        if [ ! -f 'Dockerfile' ]; then
            echo -e "\n\033[10;1;32m[WARN] `date`, Dockerfile not found!\033[0m\n"
            exit 1
        fi
        old_image_id=$(docker images |grep example_flask |awk '{print $3}')
        docker build -t example_flask .
        new_image_id=$(docker images |grep example_flask |awk '{print $3}')
        if [ "X${old_image_id}" == "X${new_image_id}" ]; then
            ## new == old
            echo -e "\n\033[10;1;32m[WARN] `date`, the image is not changed.\033[0m\n"
        elif [ ! -z ${old_image_id} ]; then
            ## remove the old one
            docker ps -f ancestor="${old_image_id}"
            docker rm -f `docker ps -f ancestor="${old_image_id}" -q`
            docker rmi -f ${old_image_id}
            # new
            docker run -d -P --name j_test_web example_flask
            docker ps -f ancestor="example_flask:latest"
        else
            # new
            docker run -d -P --name j_test_web example_flask
            docker ps -f ancestor="example_flask:latest"
        fi
