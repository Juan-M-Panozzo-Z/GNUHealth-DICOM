# GNUhealth-DICOM
GNUHealth - DICOM Web Viewer implementation

GNU Health is an extraordinary system available at https://www.gnuhealth.org/ , https://savannah.gnu.org/hg/?group=health and https://en.wikibooks.org/wiki/GNU_Health

In this repo are the files required to open DICOM images directly from GNUHealth in a Dicom Web viewer (in this case Elessar theme for Osimis Viewer https://github.com/Terabuck/Elessar). Osimis Dicom Viewer is used here because it handles the same UUID format as Orthanc, unlike the new Stone viewer that requires the "StudyInstanceUID".

To achieve this, it was only necessary to modify one line of the health_orthanc.py file (originally available in http://hg.savannah.gnu.org/hgweb/health/file/90316fe79c42/tryton/health_orthanc)

  338 add = "osimis-viewer / app / index.html? Study = {}". Format (self.uuid)

For its operation, 5 Orthanc instances are run on the same server, where only one will have full access to the Orthanc Explorer in order to receive, include or delete the files. Another will have view-only access to the Orthanc explorer. And the other three will serve as a load balancer to deliver the images to the web viewer. 

It is achieved by limiting access to the Orthanc Explorer through the NGINX server configuration, in order to prevent users from being able to load or delete studies.

After creating the .json configuration files, the 5 instances run with a single command:

$ Orthanc /root/Configuration-Balanced-A.json | Orthanc /root/Configuration-Balanced-B.json | Orthanc /root/Configuration-Balanced-C.json | Orthanc /root/Configuration-Admin.json | Orthanc /root/Configuration-Base.json
