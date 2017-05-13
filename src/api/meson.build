core_vapi = join_paths(core_build_dir, 'frida-core-@0@.vapi'.format(api_version))
core_header = join_paths(core_build_dir, 'frida-core.h')
interfaces_vapi = join_paths(interfaces_build_dir, 'frida-interfaces.vapi'.format(api_version))
interfaces_header = join_paths(interfaces_build_dir, 'frida-interfaces.h'.format(api_version))

custom_target('frida-core-header',
  input: [core, interfaces],
  output: ['frida-core.h'],
  command: [
    generate_api,
    '--output=header',
    api_version,
    facade,
    core_vapi, core_header,
    interfaces_vapi, interfaces_header,
    meson.current_build_dir(),
  ],
  install: true,
  install_dir: join_paths(get_option('includedir'), 'frida-@0@'.format(api_version)),
)

custom_target('frida-core-vapi',
  input: [core, interfaces],
  output: [
    'frida-core-@0@.vapi'.format(api_version),
    'frida-core-@0@.deps'.format(api_version)
  ],
  command: [generate_api,
    '--output=vapi',
    api_version,
    facade,
    core_vapi, core_header,
    interfaces_vapi, interfaces_header,
    meson.current_build_dir(),
  ],
  install: true,
  install_dir: join_paths(get_option('datadir'), 'vala', 'vapi'),
)
