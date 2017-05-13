test_sources = [
  'test-system.vala',
  'test-injector.vala',
  'test-agent.vala',
  'test-agent-glue.c',
  'test-gadget.vala',
  'test-host-session.vala',
  'runner.vala',
  'runner-glue.c',
  'labrats.vala',
  'async-harness.vala',
  'process.vala',
  'process-resource-usage.c',
]

subdir('labrats')

system_vala_args = []

if host_machine.system() == 'windows'
  test_sources += ['process-windows.c']
else
  test_sources += ['process-unix.c']

  system_vala_args += ['--pkg=posix']
endif

frida_tests = static_library('frida-tests', test_sources + [labrats_stamp],
  vala_args: system_vala_args,
  dependencies: [gmodule_dep, json_glib_dep, core_dep],
)

runner = executable('frida-tests', 'main.vala',
  link_with: frida_tests,
  dependencies: [core_dep],
)

test('Core', runner)
