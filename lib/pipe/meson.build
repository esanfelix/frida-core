pipe_sources = [
  'pipe.vala',
]

system_deps = []

if host_machine.system() == 'windows'
  pipe_sources += [
    'pipe-windows.c',
    'pipe-sddl.c',
  ]
elif host_machine.system() == 'darwin'
  pipe_sources += ['pipe-mach.c']
else
  pipe_sources += ['pipe-posix.c']

  system_deps += [gio_unix_dep]
endif

pipe = library('frida-pipe', pipe_sources,
  dependencies: [glib_dep, gobject_dep, gio_dep] + system_deps,
)

pipe_dep = declare_dependency(
  include_directories: include_directories('.'),
  link_with: pipe,
)
