# Pull base image.
FROM elviejokike/java:latest


ENV MATLAB_VERSION R2015b

RUN mkdir /mcr-install && cd /mcr-install &&  \
    wget -nv http://www.mathworks.com/supportfiles/downloads/${MATLAB_VERSION}/deployment_files/${MATLAB_VERSION}/installers/glnxa64/MCR_${MATLAB_VERSION}_glnxa64_installer.zip && \
    unzip MCR_${MATLAB_VERSION}_glnxa64_installer.zip && \
    ./install -mode silent -agreeToLicense yes

ENV LD_LIBRARY_PATH /usr/local/MATLAB/MATLAB_Runtime/v90/runtime/glnxa64:/usr/local/MATLAB/MATLAB_Runtime/v90/bin/glnxa64:/usr/local/MATLAB/MATLAB_Runtime/v90/sys/os/glnxa64:/usr/local/MATLAB/MATLAB_Runtime/v90/sys/os/glnxa64:

RUN rm -Rf /mcr-install

RUN apt-get update && \
    apt-get install -y libxt6 xorg xvfb