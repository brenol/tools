GOLANG_VERSION = 1.4
OS             = linux
ARCH           = amd64

VIM_BUNDLES    := ~/.vim/bundle
VIM_VUNDLE_DIR := $(VIM_BUNDLES)/Vundle.vim


all: help


help:
	@echo TODO


clean:
	@rm .essential .nodejs .golang .vimrc .vundle .ycm .go-tools > /dev/null 2>&1 || true


.essential:
	@apt-get update -qq
	@apt-get install -qqy \
					 ca-certificates curl \
					 bzr git mercurial \
					 build-essential gcc libc6-dev make cmake python-dev \
					 exuberant-ctags \
					 vim-gtk
	@touch .essential

essential: .essential


.nodejs:
	@curl -sL https://deb.nodesource.com/setup | bash -
	@apt-get install -qqy nodejs
	@touch .nodejs

nodejs: essential .nodejs


.golang:
	@curl -sSL https://storage.googleapis.com/golang/go$(GOLANG_VERSION).$(OS)-$(ARCH).tar.gz \
		| tar -v -C /usr/local -xz
	@touch .golang

golang: essential .golang


.vimrc: 
	@cat vimrc/bundles \
	     vimrc/config \
	     vimrc/mappings \
	     > ~/.vimrc
	@touch .vimrc

vimrc: .vimrc


.vundle:
	@mkdir -p $(VIM_BUNDLES)
	@git clone https://github.com/gmarik/Vundle.vim $(VIM_VUNDLE_DIR)
	@vim +'set nomore' +PluginInstall +qall
	@touch .vundle

vundle: essential vimrc .vundle


.ycm: 
	@cd $(VIM_BUNDLES)/YouCompleteMe && ./install.sh --clang-completer
	@touch .ycm

ycm: essential vundle .ycm


.go-tools:
	@go get -a -u github.com/nsf/gocode
	@go get -a -u golang.org/x/tools/cmd/goimports
	@go get -a -u code.google.com/p/rog-go/exp/cmd/godef
	@go get -a -u golang.org/x/tools/cmd/oracle
	@go get -a -u golang.org/x/tools/cmd/gorename
	@go get -a -u github.com/golang/lint/golint
	@go get -a -u github.com/kisielk/errcheck
	@go get -a -u github.com/jstemmer/gotags
	@touch .go-tools

go-tools: essential ycm golang .go-tools


install: essential golang nodejs vimrc vundle ycm go-tools
