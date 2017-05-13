project('frida-core', 'vala', 'c', 'cpp', version: '1.0.0')

c_languages = ['c', 'cpp']
if host_machine.system() == 'darwin'
  c_languages += ['objc', 'objcpp']
  add_languages('objc', 'objcpp')
endif

api_version = '1.0'

cc = meson.get_compiler('c')

cdata = configuration_data()

os_defines = [
  ['windows', 'HAVE_WINDOWS'],
  ['darwin', 'HAVE_DARWIN'],
  ['linux', 'HAVE_LINUX'],
  ['qnx', 'HAVE_QNX'],
]
foreach d : os_defines
  if d.get(0) == host_machine.system()
    cdata.set(d.get(1), 1)
  endif
endforeach

host_os = host_machine.system()

target_conditionals_prefix = '#include <TargetConditionals.h>'

is_macos_src = target_conditionals_prefix + '''
#if !TARGET_OS_MAC
# error Not macOS
#endif
'''
if cc.compiles(is_macos_src, name: 'compiling for macOS')
  host_os = 'macos'
  cdata.set('HAVE_MACOS', 1)
endif

is_ios_src = target_conditionals_prefix + '''
#if !TARGET_OS_IOS
# error Not iOS
#endif
'''
if cc.compiles(is_ios_src, name: 'compiling for iOS')
  host_os = 'ios'
  cdata.set('HAVE_IOS', 1)
endif

if cc.has_header('android/api-level.h')
  host_os = 'android'
  cdata.set('HAVE_ANDROID', 1)
endif

cpu_defines = [
  ['x86', 'HAVE_I386'],
  ['x86_64', 'HAVE_I386'],
  ['arm', 'HAVE_ARM'],
  ['arm64', 'HAVE_ARM64'],
  ['mips', 'HAVE_MIPS'],
]
foreach d : cpu_defines
  if d.get(0) == host_machine.cpu()
    cdata.set(d.get(1), 1)
  endif
endforeach

glib_dep = dependency('glib-2.0')
gobject_dep = dependency('gobject-2.0')
gmodule_dep = dependency('gmodule-2.0')
gio_dep = dependency('gio-2.0')
gee_dep = dependency('gee-0.8')
json_glib_dep = dependency('json-glib-1.0')
gum_dep = dependency('frida-gum-1.0')
gumjs_dep = dependency('frida-gumjs-1.0')

if host_machine.system() != 'windows'
  gio_unix_dep = dependency('gio-unix-2.0')
endif

backend_libs_private = []
if host_machine.system() == 'darwin'
  backend_libs_private += ['-Wl,-framework,Foundation', '-lbsm']
endif
if host_os == 'macos'
  backend_libs_private += ['-Wl,-framework,AppKit']
endif
if host_os == 'ios'
  backend_libs_private += ['-Wl,-framework,CoreGraphics', '-Wl,-framework,UIKit']
endif

resource_compiler = find_program('frida-resource-compiler')

configure_file(input: 'config.h.meson',
  output: 'config.h',
  configuration: cdata)

add_project_arguments('-include', 'config.h', language: c_languages)

vala_flags = ['--define=' + host_machine.system().to_upper()]
if host_os != host_machine.system()
  vala_flags += ['--define=' + host_os.to_upper()]
endif
add_project_arguments(vala_flags, language: ['vala'])

subdir('tools')
subdir('lib')
subdir('src')
subdir('server')
subdir('tests')
