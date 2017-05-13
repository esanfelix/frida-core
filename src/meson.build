facade = files('frida.vala')

base_sources = [
  facade,
  'frida-glue.c',
  'debugger.vala',
  'host-session-service.vala',
  'system.vala',
]

windows_sources = []
darwin_sources = []
linux_sources = []
qnx_sources = []

backend_vala_args = []
backend_deps = []

if host_machine.system() == 'darwin'
  darwin_sources = [
    'darwin/darwin-host-session.vala',
    'darwin/darwin-host-session-glue.m',
    'darwin/fruitjector.vala',
    'darwin/frida-helper-process.vala',
    'darwin/icon-helpers.m',
    'darwin/system-darwin.m',
  ]

  darwin_data = custom_target('frida-data-darwin',
    input: [
      'darwin/frida-darwin.resources',
      'darwin/agent/launchd.js',
      'darwin/agent/xpcproxy.js',
    ],
    output: [
      'frida-data-darwin.vapi',
      'frida-data-darwin.h',
      'frida-data-darwin.c',
      'frida-data-darwin-blob.S',
    ],
    command: [
      resource_compiler,
      '-c', '@INPUT0@',
      '-o', join_paths(meson.current_build_dir(), 'frida-data-darwin'),
      '@INPUT1@',
      '@INPUT2@',
    ],
  )
  darwin_sources += [darwin_data]

  helper_types = static_library('frida-helper-types', 'darwin/frida-helper-types.vala',
    dependencies: [gio_dep, interfaces_dep],
  )
  helper_types_dep = declare_dependency(link_with: helper_types)

  helper_backend_sources = [
    'darwin/frida-helper-backend.vala',
    'darwin/port.vala',
    'darwin/frida-helper-backend-glue.m',
    'darwin/port-glue.m',
  ]
  if host_os == 'ios'
    helper_backend_sources += ['darwin/springboard.m']
  endif
  helper_backend = static_library('frida-helper-backend', helper_backend_sources,
    vala_args: ['--pkg=posix'],
    dependencies: [gio_dep, gio_unix_dep, gee_dep, gum_dep, interfaces_dep, pipe_dep, helper_types_dep],
  )
  helper_backend_dep = declare_dependency(
    link_with: helper_backend,
    dependencies: [pipe_dep],
  )

  helper_sources = [
    'darwin/frida-helper-service.vala',
    'darwin/frida-helper-service-glue.m',
  ]
  helper = executable('frida-helper', helper_sources,
    vala_args: ['--pkg=posix'],
    link_args: backend_libs_private,
    dependencies: [gio_dep, gee_dep, gum_dep, interfaces_dep, pipe_dep, helper_types_dep, helper_backend_dep],
  )

  helper_process_data = custom_target('frida-data-helper-process',
    input: [
      'darwin/frida-helper-process.resources',
      helper,
    ],
    output: [
      'frida-data-helper-process.vapi',
      'frida-data-helper-process.h',
      'frida-data-helper-process.c',
      'frida-data-helper-process-blob.S',
    ],
    command: [
      resource_compiler,
      '-c', '@INPUT0@',
      '-o', join_paths(meson.current_build_dir(), 'frida-data-helper-process'),
      '@INPUT1@',
    ],
  )
  darwin_sources += [helper_process_data]

  backend_vala_args += ['--pkg=posix']
  backend_deps += [helper_types_dep, helper_backend_dep]
endif

agent_data = custom_target('frida-data-agent',
  input: [
    'agent.resources',
    agent,
  ],
  output: [
    'frida-data-agent.vapi',
    'frida-data-agent.h',
    'frida-data-agent.c',
    'frida-data-agent-blob.S',
  ],
  command: [
    resource_compiler,
    '-c', '@INPUT0@',
    '-o', join_paths(meson.current_build_dir(), 'frida-data-agent'),
    '@INPUT1@',
  ],
)

tcp_sources = [
  'tcp/tcp-host-session.vala',
]

fruity_sources = [
  'fruity/fruity-client.vala',
  'fruity/fruity-host-session.vala',
  'fruity/fruity-property-list.vala',
]
if host_machine.system() == 'windows'
  fruity_sources += ['fruity/fruity-host-session-windows.c']
elif host_machine.system() == 'darwin'
  fruity_sources += ['fruity/fruity-host-session-darwin.m']
else
  fruity_sources += ['fruity/fruity-host-session-unix.c']
endif

droidy_sources = [
  'droidy/droidy-host-session.vala',
  'droidy/droidy-client.vala',
]

local_sources = windows_sources + darwin_sources + linux_sources + qnx_sources + [agent_data]
remote_sources = tcp_sources + fruity_sources + droidy_sources

core_sources = base_sources + local_sources + remote_sources

if host_machine.system() != 'windows'
  backend_deps += [gio_unix_dep]
endif

core = library('frida-core-@0@'.format(api_version), core_sources,
  vala_args: backend_vala_args,
  vala_header: 'frida-core.h',
  link_args: backend_libs_private,
  dependencies: [glib_dep, gobject_dep, gio_dep, gee_dep, json_glib_dep, gum_dep, interfaces_dep, pipe_dep] + backend_deps,
  install: true,
)

core_dep = declare_dependency(
  include_directories: include_directories('.'),
  link_args: backend_libs_private,
  dependencies: [gio_dep, gee_dep, gum_dep, interfaces_dep] + backend_deps,
  link_with: core,
)

generate_api = files('generate-api.py')
core_build_dir = meson.current_build_dir()
subdir('api')

pkg = import('pkgconfig')
pkg.generate(filebase: 'frida-core-@0@'.format(api_version),
  name: 'Frida Core',
  version: meson.project_version(),
  description: 'Frida core library intended for static linking into bindings',
  requires: ['glib-2.0', 'gobject-2.0', 'gio-2.0', 'json-glib-1.0'],
  requires_private: ['gmodule-2.0', 'gee-0.8', 'frida-gum-1.0'],
  subdirs: 'frida-@0@'.format(api_version),
  libraries: [core],
  libraries_private: backend_libs_private,
)
