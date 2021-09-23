Bootstrap: docker
From: alpine:3.14.0
Stage: build


%post
apk add --no-cache alpine-sdk linux-headers util-linux-dev eudev-dev blkid libblkid \
                   libaio-dev keyutils-dev lz4-dev libsodium-dev userspace-rcu-dev \
                   libuuid zstd-dev pkgconf zlib-dev fuse3-dev py3-docutils

cd /opt
git clone 'https://github.com/technion/libscrypt.git'
git clone 'https://evilpiepirate.org/git/bcachefs-tools.git'

cd libscrypt
make
make install PREFIX=/usr
cd ..

cd bcachefs-tools
cat Makefile
sed -i -e 's/CFLAGS+=-DCONFIG_VALGRIND=y//g' Makefile
CFLAGS='-D__attribute_const__=__attribute__\(\(const\)\) -DPAGE_SHIFT=12' \
BCACHEFS_FUSE=1 \
make
make install
cd ..


Bootstrap: docker
From: alpine:3.14.0
Stage: final


%files from build
/usr/lib/libscrypt.so.0
/opt/bcachefs-tools/bcachefs /usr/bin/bcachefs


%post
apk add --no-cache libaio libblkid libsodium libuuid lz4-libs zstd-libs \
                   keyutils-libs fuse3 fuse3-libs userspace-rcu rsync


%runscript
exec /usr/bin/bcachefs "$@"
