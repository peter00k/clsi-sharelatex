FROM ubuntu:12.04

RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" >> /etc/apt/sources.list
RUN echo "deb http://cran.rstudio.com/bin/linux/ubuntu precise/"  >> /etc/apt/sources.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9 

RUN apt-get update
RUN apt-get -y upgrade

RUN apt-get install -y wget sudo perl

RUN wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
RUN mkdir /install-tl-unx
RUN tar -xvf install-tl-unx.tar.gz -C /install-tl-unx --strip-components=1

RUN echo "selected_scheme scheme-small" >> /install-tl-unx/texlive.profile
RUN /install-tl-unx/install-tl -profile /install-tl-unx/texlive.profile

ENV PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/texlive/2014/bin/x86_64-linux/
RUN tlmgr install latexmk

RUN useradd -m tex

RUN apt-get install -y r-base r-base-dev

ADD image/latexmkrc /usr/local/share/latexmk/LatexMk
ADD image/texmf.cnf /usr/local/texlive/2014/texmf-dist/web2c/texmf.cnf
