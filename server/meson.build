server_sources = [
  'server.vala',
  'server-glue.c',
]

system_vala_args = []

if host_machine.system() == 'darwin'
  server_sources += ['server-darwin.m']
endif

if host_machine.system() != 'windows'
  system_vala_args += ['--pkg=posix']
endif

server = executable('frida-server', server_sources,
  vala_args: system_vala_args,
  dependencies: [core_dep],
  install: true,
)
