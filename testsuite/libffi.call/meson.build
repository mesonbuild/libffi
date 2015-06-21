test_sources = ['cls_sshort']

foreach t : test_sources
  test(t, executable('t', '@0@.c'.format(t),
      include_directories : [confinc, coreinc, tinc],
      link_with : libffi,
    )
  )
endforeach
