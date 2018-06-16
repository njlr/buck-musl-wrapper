cc = read_config('cxx', 'cc') or 'cc'

remote_file(
  name = 'musl-archive', 
  out = 'musl-1.1.19.tar.gz', 
  url = 'https://www.musl-libc.org/releases/musl-1.1.19.tar.gz', 
  sha1 = '0055c906d2cea2f5f2406f4d922b7d189d406c66', 
)

genrule(
  name = 'musl-build', 
  out = 'out', 
  cmd = ' && '.join([
    'mkdir -p $OUT', 
    'tar xvzf $(location :musl-archive) --strip=1 -C $OUT', 
    'cd $OUT', 
    'export CC=' + cc, 
    './configure', 
    'make', 
  ]), 
)

genrule(
  name = 'musl-headers', 
  out = 'out', 
  cmd = ' && '.join([
    'mkdir -p $OUT', 
    'cp -r $(location :musl-build)/include/* $OUT', 
    'cp -r $(location :musl-build)/obj/include/* $OUT', 
  ]), 
)

genrule(
  name = 'musl-static-lib', 
  out = 'libc.a', 
  cmd = 'cp $(location :musl-build)/lib/libc.a $OUT', 
)

genrule(
  name = 'musl-shared-lib', 
  out = 'libc.so', 
  cmd = 'cp $(location :musl-build)/lib/libc.so $OUT', 
)

prebuilt_cxx_library(
  name = 'musl', 
  header_namespace = '', 
  header_dirs = [
    ':musl-headers', 
  ], 
  preferred_linkage = 'static', 
  static_lib = ':musl-static-lib', 
  shared_lib = ':musl-shared-lib', 
  visibility = [
    'PUBLIC', 
  ], 
)

cxx_binary(
  name = 'hello', 
  srcs = [
    'hello.c', 
  ],
  compiler_flags = [
    '-nostdinc', 
    '-nodefaultlibs', 
    '-nostdlib', 
  ],
  deps = [
    ':musl', 
  ], 
)
