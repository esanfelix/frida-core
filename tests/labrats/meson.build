labrat_files = []

if host_os == 'macos'
  labrat_files += [
    'sleeper-macos',
    'sleeper-macos32',
    'stdio-writer-macos',
    'stdio-writer-macos32',
    'simple-agent-macos.dylib',
    'resident-agent-macos.dylib',
  ]
endif

labrats = custom_target('labrats',
  input: labrat_files,
  output: labrat_files,
  command: ['cp', '@INPUT@', '@OUTDIR@'],
)
labrats_stamp = custom_target('labrats-stamp',
  input: [labrats],
  output: '.stamp',
  command: ['touch', '@OUTPUT@'],
)
