FROM ubuntu:14.04 

#Get latest packaged version of Mono to bootstrap
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
RUN echo "deb http://download.mono-project.com/repo/debian wheezy main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list

RUN apt-get update
RUN apt-get install -y mono-devel mono-mcs
RUN apt-get install -y libtool autoconf g++ gettext make git

RUN git clone https://github.com/mono/mono --branch mono-4.2.0-branch --single-branch
RUN cd mono && \
    ./autogen.sh   --prefix /opt/mono && \
    make -j 4 && \
    make install

#cleanup
RUN apt-get purge -y mono-devel mono-gmcs libtool autoconf g++ gettext
RUN apt-get autoremove -y
RUN cd .. && rm -rf mono

#install pcl reference assemblies
RUN apt-get install -y wget unzip
WORKDIR /root
RUN wget https://github.com/directhex/xamarin-referenceassemblies-pcl/archive/master.zip
RUN unzip master.zip
RUN mkdir /opt/mono/lib/mono/xbuild-frameworks/.NETPortable/
RUN cp -r /root/xamarin-referenceassemblies-pcl-master/* /opt/mono/lib/mono/xbuild-frameworks/.NETPortable/

#cleanup
RUN rm -rf master.zip
RUN rm -rf xamarin-referenceassemblies-pcl-master

#mono doesn't ship v4.5.2 libraries yet, just symlink (yeah, a little evil)
RUN ln -s /opt/mono/lib/mono/xbuild-frameworks/.NETFramework/v4.5.1/ /opt/mono/lib/mono/xbuild-frameworks/.NETFramework/v4.5.2

#set environement variables for custom mono path /opt/mono
ENV MONO_PREFIX /opt/mono
ENV GNOME_PREFIX /opt/gnome
ENV DYLD_FALLBACK_LIBRARY_PATH $MONO_PREFIX/lib:$DYLD_LIBRARY_FALLBACK_PATH
ENV LD_LIBRARY_PATH $MONO_PREFIX/lib:$LD_LIBRARY_PATH
ENV C_INCLUDE_PATH $MONO_PREFIX/include:$GNOME_PREFIX/include
ENV ACLOCAL_PATH $MONO_PREFIX/share/aclocal
ENV PKG_CONFIG_PATH $MONO_PREFIX/lib/pkgconfig:$GNOME_PREFIX/lib/pkgconfig
ENV PATH $MONO_PREFIX/bin:$PATH
ENV PS1 "[mono] \w @ " 


RUN mozroots --import --sync
