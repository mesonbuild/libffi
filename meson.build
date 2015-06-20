project('libffi', 'c')

cc = meson.get_compiler('c')
cdata = configuration_data()

check_headers = [
  ['HAVE_ALLOCA_H', 'alloca.h'],
  ['HAVE_DLFCN_H', 'dlfcn.h'],
  ['HAVE_INTTYPES_H', 'inttypes.h'],
  ['HAVE_MEMORY_H', 'memory.h'],
  ['HAVE_STDINT_H', 'stdint.h'],
  ['HAVE_STDLIB_H', 'stdlib.h'],
  ['HAVE_STRINGS_H', 'strings.h'],
  ['HAVE_STRING_H', 'string.h'],
  ['HAVE_SYS_MMAN_H', 'sys/mman.h'],
  ['HAVE_SYS_STAT_H', 'sys/stat.h'],
  ['HAVE_SYS_TYPES_H', 'sys/types.h'],
  ['HAVE_UNISTD_H', 'unistd.h'],
]

foreach h : check_headers
  if cc.has_header(h.get(1))
    cdata.set(h.get(0), 1)
  endif
endforeach

check_functions = [
# check token HAVE_ALLOCA
# check token HAVE_AS_ASCII_PSEUDO_OP
# check token HAVE_AS_CFI_PSEUDO_OP
# check token HAVE_AS_REGISTER_PSEUDO_OP
# check token HAVE_AS_SPARC_UA_PCREL
# check token HAVE_AS_STRING_PSEUDO_OP
# check token HAVE_AS_X86_64_UNWIND_SECTION_TYPE
# check token HAVE_AS_X86_PCREL
# check token HAVE_HIDDEN_VISIBILITY_ATTRIBUTE
# check token HAVE_LONG_DOUBLE
# check token HAVE_LONG_DOUBLE_VARIANT
  ['HAVE_MEMCPY', 'memcpy', '#include<string.h>'],
# check token HAVE_MKOSTEMP
  ['HAVE_MMAP', 'mmap', '#include<sys/mman.h>'],
# check token HAVE_MMAP_ANON
# check token HAVE_MMAP_DEV_ZERO
# check token HAVE_MMAP_FILE
# check token HAVE_RO_EH_FRAME
# check token HAVE_HIDDEN_VISIBILITY_ATTRIBUTE
]

foreach f : check_functions
  if cc.has_function(f.get(1), prefix : f.get(2))
    cdata.set(f.get(0), 1)
  endif
endforeach

cdata.set('SIZEOF_DOUBLE', cc.sizeof('double'))
cdata.set('SIZEOF_LONG_DOUBLE', cc.sizeof('long double'))
cdata.set('SIZEOF_SIZE_T', cc.sizeof('size_t'))

cdata.set('HAVE_LONG_DOUBLE', 1)
cdata.set('HAVE_LONG_DOUBLE_VARIANT', 0)
cdata.set('FFI_EXEC_TRAMPOLINE_TABLE', 0)

ptrsize = cc.sizeof('void*')

ffi_sources = [
  'src/prep_cif.c',
  'src/types.c',
  'src/raw_api.c',
  'src/java_raw_api.c',
  'src/closures.c'
]

# Only support x86 and x86_64 for now.

if build.name() == 'darwin'
  if ptrsize == 8
    plat_sources = ['src/x86/ffi.c',
      'src/x86/darwin.S',
      'src/x86/ffi64.c',
      'src/x86/darwin64.S'
    ]
    cdata.set('TARGET', 'X86_DARWIN')
    tinc = include_directories('src/x86')
  else
    plat_sources = ['src/x86/win32.S']
    cdata.set('TARGET', 'X86_DARWIN32')
    tinc = include_directories('src/x86')
  endif
elif build.name() == 'windows'
  if ptrsize == 8
    plat_sources = ['src/x86/ffi.c', 'src/x86/win64.S']
    cdata.set('TARGET', 'X86_WIN64')
    tinc = include_directories('src/x86')
  else
    plat_sources = ['src/x86/ffi.c', 'src/x86/win32.S']
    cdata.set('TARGET', 'X86_WIN32')
    tinc = include_directories('src/x86')
  endif
else
  if ptrsize == 8
    plat_sources = ['src/x86/ffi64.c',
    'src/x86/unix64.S',
    'src/x86/ffi.c',
    'src/x86/sysv.S']
    cdata.set('TARGET', 'X86_64')
    tinc = include_directories('src/x86')
  else
    plat_sources = ['src/x86/ffi.c', 'src/x86/sysv.S', 'src/x86/win32.S']
    cdata.set('TARGET', 'X86')
    tinc = include_directories('src/x86')
  endif
endif

if get_option('shared_lib')
  libtype = 'shared_library'
else
  libtype = 'static_library'
endif

confinc = include_directories('.')
coreinc = include_directories('include')
subdir('include')

configure_file(input : 'fficonfig.h.meson',
  output : 'fficonfig.h',
  configuration : cdata)

libffi = build_target('ffi',
  ffi_sources, plat_sources,
  target_type : libtype,
  include_directories : [coreinc, tinc],
)

subdir('testsuite')
