FROM swiftdocker/swift
# Set Environment Variables & Language Environment
ENV HOME /root


RUN apt-get clean && apt-get update && apt-get install -y locales supervisor
RUN locale-gen en_US.UTF-8  
ENV LANG en_US.UTF-8  
ENV LANGUAGE en_US:en  
ENV LC_ALL en_US.UTF-8


RUN apt-get update && apt-get -y install openssh-server supervisor unzip npm nodejs
RUN mkdir /var/run/sshd
RUN echo 'root:alpine' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# Install Basic Packages
RUN apt-get install -y  unzip nano tmux colord zsh emacs


#install ZSH
RUN git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh \
      && cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc \
      && chsh -s /bin/zsh
RUN TERM=xterm-256color

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile


# Run updates
RUN apt-get update --fix-missing && apt-get install -y

# Install Curl
RUN apt-get install curl -y 


# Install supervisor
RUN apt-get install python  nginx  -y
RUN curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
RUN python get-pip.py
RUN pip install supervisor

# Start the supervisor instance when the machine autostarts
RUN echo "supervisord -c /etc/supervisord.conf" >> /root/.bashrc

# Copy config file for Supervisor (Custom supervisord.conf)
# see https://gist.github.com/johndpope/01a74ffb93921df48e334097117be1b3 for actual file.   supervisord.conf
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf


ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 8.6.0

SHELL ["/bin/bash", "-c"]
# Install nvm with node and npm
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.29.0/install.sh | bash \
    && source $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default

# Set up our PATH correctly so we don't have to long-reference npm, node, &c.
ENV NODE_PATH $NVM_DIR/versions/node/v$NODE_VERSION/lib/node_modules
ENV PATH      $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

RUN echo 55 && git clone --recursive https://github.com/johndpope/public_api.git
RUN cd /public_api && npm install
RUN cd /public_api/etherdelta.github.io && npm install
ADD config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ADD config/nginx.conf /etc/nginx/nginx.conf
ADD sites-enabled/default /etc/nginx/sites-enabled/default
#ADD certs /certs

EXPOSE 80
EXPOSE 443
EXPOSE 22

CMD ["/usr/bin/supervisord"]
