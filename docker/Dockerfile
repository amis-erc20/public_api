FROM swiftdocker/swift
# Set Environment Variables & Language Environment
ENV HOME /root


RUN apt-get clean && apt-get update && apt-get install -y locales supervisor
RUN locale-gen en_US.UTF-8  
ENV LANG en_US.UTF-8  
ENV LANGUAGE en_US:en  
ENV LC_ALL en_US.UTF-8


RUN apt-get update && apt-get -y install openssh-server supervisor unzip
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

RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf



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
