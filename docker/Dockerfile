FROM ubuntu:14.04
# Set Environment Variables & Language Environment
ENV HOME /root
RUN locale-gen en_US.UTF-8  
ENV LANG en_US.UTF-8  
ENV LANGUAGE en_US:en  
ENV LC_ALL en_US.UTF-8


RUN apt-get upgrade
RUN apt-get update
RUN apt-get -y install software-properties-common curl build-essential unzip
RUN curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
RUN apt-get -y install git supervisor nginx nodejs
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/*

# SSH keys nonsense
RUN echo "IdentityFile ~/.ssh/id_rsa" >> /etc/ssh/ssh_config
RUN mkdir /root/.ssh
RUN ssh-keyscan -t rsa github.com 2>&1 >> /root/.ssh/known_hosts


# Install Basic Packages
RUN apt-get install -y  unzip nano tmux colord zsh emacs


#install ZSH
RUN git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh \
      && cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc \
      && chsh -s /bin/zsh
RUN TERM=xterm-256color

RUN echo 55 && git clone --recursive https://github.com/johndpope/public_api.git
RUN cd /public_api && npm install
RUN cd /public_api/etherdelta.github.io && npm install
ADD config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ADD config/nginx.conf /etc/nginx/nginx.conf
ADD sites-enabled/default /etc/nginx/sites-enabled/default
#ADD certs /certs

EXPOSE 80
EXPOSE 443

CMD ["/usr/bin/supervisord"]
