#LArLite: Event viewer

##Instructions on the web
[Corey's instructions](https://github.com/coreyjadams/EventViewer)

[Redmine](https://cdcvs.fnal.gov/redmine/projects/ubooneoffline/wiki/Larlite_Evd)

You basically need to install both `sip` and `pyqt` (probably from source)

##Notes
McObjects are not visualized in the `evd.py` viewer, so you'll have to use the `3dviewerevd3d.py`.
That requires also OpenGL for python, which you can install with:

```
pip install PyOpenGL PyOpenGL_accelerate
```