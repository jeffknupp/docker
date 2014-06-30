FROM ubuntu:14.04
MAINTAINER Jeff Knupp <jeff@jeffknupp.com>

# Update repo
RUN apt-get update

# Install zshell
RUN apt-get -yq install zsh git

# Create user
RUN useradd -m -g sudo -s /bin/zsh jknupp

RUN apt-get install -yq openssh-server
RUN mkdir /var/run/sshd

# Install vim
RUN apt-get -yq install vim curl

RUN echo "temp123\ntemp123" | passwd jknupp

EXPOSE 22

USER jknupp

WORKDIR /home/jknupp

# Set up zsh and vim configuration
RUN git clone --recursive https://www.github.com/jeffknupp/prezto.git
RUN ln -s .zprezto/runcoms/zshenv .zshenv
RUN ln -s .zprezto/runcoms/zlogin .zlogin
RUN ln -s .zprezto/runcoms/zlogout .zlogout
RUN ln -s .zprezto/runcoms/zprofile .zprofile
RUN ln -s .zprezto/runcoms/zpreztorc .zpreztorc

# Clone config files 
RUN mkdir -p code/github_code
RUN git clone https://www.github.com/jeffknupp/config_files.git code/github_code/config_files
RUN ln -s code/github_code/config_files/.vimrc
RUN ln -s code/github_code/config_files/.zshrc


# Install Powerline
RUN mkdir tools
RUN git clone https://www.github.com/lokaltog/powerline.git tools/powerline

RUN git clone https://github.com/gmarik/Vundle.vim.git /home/jknupp/.vim/bundle/Vundle.vim

# Create .zshrc.include file to prepare for manually installed tools
RUN echo "export PATH=/home/jknupp/install/bin:$PATH\nexport LD_LIBRARY_PATH=/home/jknupp/lib:$LD_LIBRARY_PATH\n" > /home/jknupp/.zshrc.include

WORKDIR code/github_code 

RUN for repo in `curl https://api.github.com/users/jeffknupp/repos  | grep clone_url | awk -F " " '{print $2}' | sed "s/\"//g" | sed "s/,//g"`; do git clone $repo; done

WORKDIR /home/jknupp
RUN echo "To set up your vim environment, run the command :PluginInstall after launching vim." >> README

USER root
CMD    ["/usr/sbin/sshd", "-D"]
