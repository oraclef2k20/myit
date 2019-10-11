

OS

amzlinux2

development environment
--
```
sudo yum update -y
```
```
sudo reboot
```

```
mkdir ~/workdir
```

install jq 1.6
```
sudo curl -SsL -o jq https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64
sudo chmod +x jq
sudo mv jq /usr/local/bin/jq
```


install golang
```
sudo rpm --import https://mirror.go-repo.io/centos/RPM-GPG-KEY-GO-REPO
curl -s https://mirror.go-repo.io/centos/go-repo.repo | sudo tee /etc/yum.repos.d/go-repo.repo
sudo sed -e 's/\$releasever/7/' -i /etc/yum.repos.d/go-repo.repo 
sudo yum install -y gcc
sudo yum install -y golang --disablerepo=amzn2-core,amzn2extra-golang1.9 --enablerepo=go-repo
```

install git 2.23.0
```
sudo yum install -y git zlib-devel autoconf 
```
```
cd ~/workdir
git clone -b v2.23.0 https://github.com/git/git
cd git
```
```
make configure
./configure --prefix=/usr
make all
```

```
sudo yum remove -y git
```
```
sudo make install
```


docker environment
--

Independent docker that is new version 

```
sudo amazon-linux-extras install -y docker
```
```
sudo usermod -a -G docker $(whoami)
# newgrp docker   re-login
```

for building docker enging with DinD
```
cat <<'EOF' | sudo tee /etc/docker/daemon.json
{
    "experimental": true
}
EOF
```

```
sudo systemctl start docker
```

```
sudo yum install -y device-mapper-devel
```

```
cd ~/workdir
git clone -b v19.03.3 https://github.com/docker/engine.git
cd engine
```


`[+] Building 162.6s (67/67) FINISHED`
```
make
```

```
sudo GOPATH=$(go env GOPATH) make install
```

```
sudo systemctl stop docker
```


for root to excute command in /usr/local/bin
```
sudo sed -r -e 's|^(Defaults\s+secure_path.*)|\1:/usr/local/bin|' -i /etc/sudoers
```

run service of docker
```
nohup sudo /usr/local/bin/dockerd -D > /dev/null &
```



install docker cli

```
cd ~/workdir
git clone -b v19.03.3  https://github.com/docker/cli.git
cd cli
```
```
make -f docker.Makefile binary
sudo mv build/docker* /usr/local/bin/
```

```
echo 'export PATH=/usr/local/bin:$PATH' >> ~/.bashrc
source  ~/.bashrc
```

```
cat /etc/docker/daemon.json | jq '.|= .+{"features":{"buildkit":true}}'| sudo tee /etc/docker/daemon.json
```

```json
{
  "experimental": true,
  "features": {
    "buildkit": true
  }
}
```

```
sudo kill -HUP $(pgrep dockerd)
```

```
cd ~/workdir
mkdir -p ~/.docker/cli-plugins/
mkdir -p testdir && cd testdir
```

```
docker build --platform=local -o . git://github.com/docker/buildx
mv buildx ~/.docker/cli-plugins/docker-buildx
```




javascript  environment
--
- nvm
- node
- typescript
- yarn

```
curl -SsL -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.0/install.sh | bash
source ~/.bashrc
nvm install --lts
nvm install node
nvm alias default v12.11.1
```



for tsuquyomi
```
npm -g install typescript
```

```
npm -g install yarn
```

editor environemnt
--


- vim

- for vscode

- syntax

```
sudo yum install -y python3-devel ncurses-devel
```
```
cd ~/workdir
git clone -b v8.1.2135 https://github.com/vim/vim.git
cd vim
```
```
 ./configure --enable-python3interp
make
```
```
sudo make install
```
```
sudo yum remove -y vim-enhanced vim-common vim-filesystem
```

vim plugins(vundle)
```
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```


`~/.vimrc`

```vim
set nocompatible              " be iMproved, required
filetype off                  " required

set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'VundleVim/Vundle.vim'

"----custom-start---
Plugin 'prettier/vim-prettier'
"Plug 'prettier/vim-prettier', {
"  \ 'do': 'yarn install',
"  \ 'for': ['javascript', 'typescript', 'css', 'less', 'scss', 'json', 'graphql', 'markdown', 'vue', 'yaml', 'html'] }
" cd ~/.vim/bundle/vim-prettier/ && yarn install && cd -
let g:prettier#autoformat = 0
let g:prettier#config#trailing_comma = 'all'
autocmd BufWritePre *.js,*.jsx,*.mjs,*.ts,*.tsx,*.css,*.less,*.scss,*.json,*.graphql,*.md,*.vue,*.yaml,*.html PrettierAsync


Plugin 'vim-airline/vim-airline'
Plugin 'vim-airline/vim-airline-themes'
" airline
let g:airline#extensions#tabline#enabled = 1
let g:airline_theme='violet'
let g:airline_powerline_fonts = 1

Plugin 'fatih/vim-go'
"Plug 'fatih/vim-go', {'do': ':GoInstallBinaries'}
let g:go_list_type = "quickfix"
let g:go_fmt_command = "goimports"
let g:go_highlight_functions = 1
let g:go_highlight_methods = 1
let g:go_highlight_structs = 1
let g:go_highlight_operators = 1
let g:go_highlight_fields = 1
let g:go_highlight_variable_declarations = 1
let g:go_highlight_variable_assignments = 0
let g:go_highlight_string_spellcheck = 1
let g:go_highlight_types = 1
let g:go_highlight_function_parameters = 1
let g:go_highlight_extra_types = 1

" tsuquyomi
Plugin 'quramy/tsuquyomi'
autocmd FileType typescript setlocal completeopt+=menu,preview
" npm -g install typescript"

" numbertoggle
Plugin 'jeffkreeftmeijer/vim-numbertoggle'
set number relativenumber

" deoplete
Plugin 'Shougo/deoplete.nvim'
Plugin 'roxma/nvim-yarp'
Plugin 'roxma/vim-hug-neovim-rpc'
" pip3 install --user pynvim
let g:deoplete#enable_at_startup = 1

"----custom-end----

call vundle#end()            " required
filetype plugin indent on    " required
syntax enable

```




aws environment
--

install and configure awscli

```
pip3 install --user awscli
```
```
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

```
aws configure set aws_access_key_id xxxxxx
aws configure set aws_secret_access_key yyyyy
aws configure set cli_follow_urlparam false
```


`myprofile=myprofile`
`myses=mysessionname`
```
aws configure --profile $myprof set role_arn arn:aws:iam::000000000000:role/assumegroup
aws configure --profile $myprof set source_profile  default
aws configure --profile $myprof set role_session_name  $myses
```
```
echo "export AWS_DEFAULT_PROFILE=$myprof" >> ~/.bashrc
```
```
source ~/.bashrc
```

check your iam
```
aws sts get-caller-identity
```


completion for fish
```
omf install aws
```
Or attach a IAM role that can operate as a administrator.

