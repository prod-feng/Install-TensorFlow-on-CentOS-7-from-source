# Install-TensorFlow-on-CentOS-7-from-source
Install TensorFlow on CentOS 7 from source

Summary:
  OS: CentOS 7.4.1708, 3.10.0-693.17.1.el7.x86_64
  tensorflow-1.12
  conda version : 4.6.4
  conda-build version : 3.17.6
  python version : 3.7.1.final.0
  bazel                     0.18.0 
  
  In the folowing I list the steps that I used to install TensorFlow on CentOS 7 from source.
  
1. Install anacaonda3, and other needed packages
   There are a lot of online docs about how to install anaconda, so I skip the details of this step. One thing need to mention is: if possible, use conda to install the packages. I found that some pip packages are fixed to be install to /usr folder, so may cause trouble for HPC cluster users, who have no written previlege on that.
   
   >conda install six wheel mock
   
2. Install Bazel, which is the building tool used by TensorFlow.

   >conda install bazel
  
   and other packages:
   >pip install -U  keras_applications==1.0.6 --no-deps
   >pip install -U  keras_preprocessing==1.0.5 --no-deps
  
3. Download the source code of TensorFlow:
    (https://github.com/tensorflow/tensorflow/releases/tag/v1.12.0)
   
   >tar zxf v1.12.0.tar.gz
   
   >cd tensorflow-1.12.0
   
   >./configure
   >
   >bazel build --config=opmonolithic  //tensorflow/tools/pip_package:build_pip_package
   
   There are some changes on the building settings may needed to compile sucessfully. First, it seems TensorFlow does not compile on Python 3.7, because an issue in "protobuf" package.
   It is supposed to be fixed in "protobuf" of new version on 3.6.1. The problem is that the bazel config file of TensorFlow, "tensorflow/workspace.bzl",
   is hard coded to use protobuf 3.6.0, so you need to manually change it, like the following:
>     PROTOBUF_URLS = [
>
>        "https://mirror.bazel.build/github.com/google/protobuf/archive/v3.6.1.3.tar.gz",
>        "https://github.com/google/protobuf/archive/v3.6.1.3.tar.gz",
>     ]
>     PROTOBUF_SHA256 = "73fdad358857e120fd0fa19e071a96e15c0f23bb25f85d3f7009abfd4f264a2a"
>     PROTOBUF_STRIP_PREFIX = "protobuf-3.6.1.3"

Do not forget update the "PROTOBUF_SHA256" key when change diffrent version of package.

4. If everything goes fine when compiled, then you will be able to build a pip package of the TnesorFlow. 
   > ./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
   
After it is done, you can then install the build TensorFlow package:

   >pip install /tmp/tensorflow_pkg//tensorflow-1.12.0-cp37-cp37m-linux_x86_64.whl
   
Great. It's done. You can verify to see the install TensorFLow:

   >$ pip list|grep tensorflow
   
     tensorflow                         1.12.0 
   
