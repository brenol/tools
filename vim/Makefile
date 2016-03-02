GOLANG_VERSION = 1.6
GO_ROOT = /usr/local/go

VIM_BUNDLES    := ~/.vim/bundle
VIM_VUNDLE_DIR := $(VIM_BUNDLES)/Vundle.vim

UNAME_S := $(shell uname -s)
ifeq ($(UNAME_S),Linux)
	OS   		   = linux
	GOLANG_ARCH    = amd64
	GOLANG_PKG_FMT = tar.gz
endif
ifeq ($(UNAME_S),Darwin)
	OS             = darwin
	GOLANG_ARCH    = amd64-osx10.8
	GOLANG_PKG_FMT = pkg
endif

ifeq ($(OS),)
	$(error ✗ OS unknown)
endif



all: help


help:
	$(info TODO: HELP MESSAGE)


clean:
	@rm .essential .nodejs .golang .vimrc .vundle .ycm .go-tools > /dev/null 2>&1 || true
	$(info ✓ Cache is clean)


clean-vimrc:
	@rm .vimrc > /dev/null 2>&1 || true


.essential:
	$(info --- Installing Essential packages and tools)

ifeq ($(OS),linux)
	@sudo apt-get update -qq
	@sudo apt-get install -qqy \
		 ca-certificates curl \
		 bzr git mercurial \
		 build-essential gcc libc6-dev make cmake python-dev \
		 exuberant-ctags \
		 vim-gtk
endif

ifeq ($(OS),darwin)
	@brew update
	@brew install git mercurial cmake ctags curl || true
	@brew unlink vim > /dev/null 2>&1 || true
	@brew install vim --with-lua --override-system-vi --HEAD
	@brew link vim
endif

	@touch .essential

essential: .essential
	$(info ✓ Essential packages and tools installed)


.nodejs:
	$(info --- Installing NodeJS)

ifeq ($(OS),linux)
	@curl -sL https://deb.nodesource.com/setup | sudo bash -
	@sudo apt-get install -qqy nodejs
endif

ifeq ($(OS),darwin)
	@brew unlink node > /dev/null 2>&1 || true
	@brew install node
	@brew link node
endif

	@touch .nodejs

nodejs: essential .nodejs
	$(info ✓ NodeJS installed)


GOLANG_FILE := go$(GOLANG_VERSION).$(OS)-$(GOLANG_ARCH).$(GOLANG_PKG_FMT)
GOLANG_URL := https://storage.googleapis.com/golang/$(GOLANG_FILE)

.golang:
	$(info --- Installing Go)

ifeq ($(OS),linux)
	@curl -sSL $(GOLANG_URL) | sudo tar -v -C /usr/local -xz
endif

ifeq ($(OS),darwin)
	@curl -o $(GOLANG_FILE) $(GOLANG_URL)
	@sudo installer -pkg $(GOLANG_FILE) -target /
	@rm $(GOLANG_FILE)
	@export GOROOT=$(GOROOT)
	@export PATH=$(PATH):$(GOROOT)/bin
endif

	@touch .golang

golang: essential .golang
	$(info ✓ Go installed)


.vimrc: 
	$(info --- Installing full vimrc)
	@cat vimrc/basic \
	     vimrc/bundles \
	     vimrc/config \
	     vimrc/mappings \
	     vimrc/local \
	     > ~/.vimrc
	@touch ~/.vimrc.local
	@touch .vimrc

vimrc: .vimrc
	$(info ✓ Full vimrc installed)

vimrc-basic:
	@cat vimrc/basic vimrc/local > ~/.vimrc
	@touch ~/.vimrc.local
	$(info ✓ Basic vimrc installed)

.vundle:
	$(info --- Installing Vundle)
	@mkdir -p $(VIM_BUNDLES)
	@git clone https://github.com/gmarik/Vundle.vim $(VIM_VUNDLE_DIR)
	@vim +'set nomore' +PluginInstall +qall
	@touch .vundle

vundle: essential vimrc .vundle
	$(info ✓ Vundle installed)

.ycm: 
	$(info --- Installing YouCompleteMe)
	@cd $(VIM_BUNDLES)/YouCompleteMe && ./install.py --clang-completer
	@touch .ycm

ycm: essential vundle .ycm
	$(info ✓ YouCompleteMe installed)

.go-tools:
	$(info --- Installing Go-Tools)
ifeq ($(GOPATH),)
	$(error ✗ Your GOPATH is not set properly)
else
	@rm -rf $(GOPATH)/src/golang.org/x/tools || true
	@vim +GoInstallBinaries +qall
	@touch .go-tools
endif

go-tools: essential ycm golang .go-tools
	$(info ✓ Go-Tools installed)	


install: essential golang nodejs vimrc vundle ycm go-tools
	$(info ✓ VIM installed)


update: clean-vimrc vundle
	@vim +'set nomore' +PluginClean +PluginInstall +PluginUpdate +qall
	$(info ✓ VIM updated)
