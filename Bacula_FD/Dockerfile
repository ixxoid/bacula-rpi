FROM bacula-common-rpi:11.0.6

EXPOSE 9102/tcp

ENTRYPOINT ["bacula-fd"]

CMD ["-f", "-c", "/opt/bacula/etc/bacula-fd.conf"]
