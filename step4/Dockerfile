FROM ubuntu:wily

ENV DEBIAN_FRONTEND noninteractive

ADD install.sh install.sh
RUN sh ./install.sh && rm install.sh
RUN useradd -ms /bin/bash octave
ADD *.m /home/octave/
RUN chown -R octave:octave /home/octave/

USER octave
WORKDIR /home/octave

VOLUME ["/source"]
ENTRYPOINT ["octave"]