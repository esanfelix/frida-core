agent_sources = [
  'agent.vala',
  'agent-glue.c',
  'script-engine.vala',
]

if not get_option('enable_diet')
  agent_sources += ['dummy.cpp']
endif

agent = shared_library('frida-agent', agent_sources,
  name_prefix: '',
  dependencies: [gio_dep, gee_dep, gum_dep, gumjs_dep, interfaces_dep, pipe_dep],
)

agent_dep = declare_dependency(link_with: agent)
