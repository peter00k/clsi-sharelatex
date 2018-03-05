FROM ubuntu:16.04

#RUN echo "deb http://archive.ubuntu.com/ubuntu xenial main universe" >> /etc/apt/sources.list
RUN echo "deb http://cran.rstudio.com/bin/linux/ubuntu xenial/"  >> /etc/apt/sources.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9

RUN apt-get update
RUN apt-get -y upgrade

RUN apt-get install -y wget perl libfontconfig ghostscript libxt6 libxext6 gnuplot python-setuptools r-base r-base-dev libx11-dev freeglut3 freeglut3-dev libxml2-dev libcurl4-openssl-dev time strace

RUN wget -nv http://ctan.sharelatex.com/tex-archive/systems/texlive/tlnet/install-tl-unx.tar.gz
RUN mkdir /install-tl-unx
RUN tar -xvf install-tl-unx.tar.gz -C /install-tl-unx --strip-components=1

RUN echo "selected_scheme scheme-full" >> /install-tl-unx/texlive.profile
RUN /install-tl-unx/install-tl -repository http://ctan.sharelatex.com/tex-archive/systems/texlive/tlnet/ -profile /install-tl-unx/texlive.profile && rm -rf /usr/local/texlive/2017/texmf-dist/doc/

RUN useradd -m tex

RUN easy_install Pygments

RUN Rscript -e "install.packages(c('knitr', 'ggplot2', 'xtable', 'car', 'reshape2', 'plyr', 'data.table', 'RColorBrewer', 'dplyr', 'MASS', 'lme4', 'e1071', 'GeneNet', 'colorspace', 'glasso', 'glmnet', 'lars', 'lattice', 'maptools', 'Matrix', 'mvtnorm', 'Rcpp', 'reshape2', 'RGraphics', 'SparseM', 'foreach','moments','PerformanceAnalytics','PortfolioAnalytics','quantmod','scales','xts','yuima','zoo'), dependencies = TRUE, Ncpus = as.numeric(system('nproc --ignore=1',intern=TRUE)), repos='http://cran.rstudio.com'); source('http://bioconductor.org/biocLite.R'); biocLite(c('pcaMethods', 'Biobase'))"

RUN apt-get install -y libosmesa-dev
RUN wget -nv http://sourceforge.net/projects/asymptote/files/2.41/asymptote-2.41.src.tgz; tar -xvf asymptote-2.41.src.tgz; cd asymptote-2.41; ./configure --enable-offscreen; make -j$(nproc --ignore=1);
ENV PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/texlive/2017/bin/x86_64-linux/
ENV ASYMPTOTE_TEXPATH /usr/local/texlive/2017/bin/x86_64-linux/
RUN cd asymptote-2.41; make install-asy

# Not needed until we want to support markdown
#RUN apt-get install -y haskell-platform
#RUN cabal update; cabal install --global pandoc
#RUN cabal install --global pandoc-citeproc

ADD image/latexmkrc /usr/local/share/latexmk/LatexMk
ADD image/texmf.cnf /usr/local/texlive/2017/texmf-dist/web2c/texmf.cnf
RUN apt-get install chktex
ADD image/run-chktex.sh /usr/bin/run-chktex.sh
RUN chmod +x /usr/bin/run-chktex.sh

RUN apt-get install -y inkscape

# Set the path for final use
ENV PATH /usr/local/texlive/2017/bin/x86_64-linux:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Need latexmk 4.52c (19 Jan 2017) to fix jobname with xelatex
RUN /usr/local/texlive/2017/bin/x86_64-linux/tlmgr update latexmk

# Install hebrew support
RUN wget -nv https://github.com/sharelatex/texmf-local/archive/master.tar.gz ; tar --strip-components=1 -C /usr/local/texlive/texmf-local -x -v -z -f master.tar.gz
RUN mktexlsr && updmap-sys

# Try setting up locale
ENV LANG C.UTF-8

# Set up fonts for use by xetex
RUN cp `kpsewhich --var-value TEXMFSYSVAR`/fonts/conf/texlive-fontconfig.conf /etc/fonts/conf.d/09-texlive.conf && fc-cache -fsv

# Create lualatex font cache for speed
RUN luaotfload-tool --update

# Install qpdf
RUN apt-get install -y qpdf

# Remove setuid/setgid permissions
RUN find / -xdev \( -perm -4000 -o -perm -2000 \) -type f -execdir chmod -v ug-s '{}' \;

# Regenerate the font config cache as the last thing we do:
RUN fc-cache -s
