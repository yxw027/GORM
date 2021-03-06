# Changelog for GPSFTP5 scripts

## 2020-02-03	sjm	sbf2rin unpack bugfix (issue #19)
- Copy rawfile to unpackdir before unpacking using sbf2rin

## 2020-02-02	sjm	Parallel instanses.
- Now runs directly from clone directory.
- Able to run along side another instanse of the scripts (named instanses).

## 2020-01-25	sjm	Dated position, parallel installations.
- Position is now dated like antennas and receivers. Entry from positions, antennas and receivers is mached on date only, not time.

## 2020-01-22	sjm	Gzipped SBF files
- Handle inbound gzipped SBF files

## 2020-01-22	sjm	sbf2rin file pickup
- Fixed a bug finding files extracted by sbf2rin.

## 2020-01-20	sjm	Reprocess bugfix
- Fixed a bug in admin.cgi regarding reprocessing several doy's for a site.

## 2020-01-16	sjm	Distribution type Raw inplemented.
- Distribution type 'Raw' distributes the unmodified files arrived in $INCOMING.

## 2020-01-01	sjm	Implement splicing of sub-hour Rinex files
- Sub-hour files are ignored for now. Requires extensive change in code to implement.
- Conform to Perl recommendation for calling contructors.

## 2019-12-30	sjm	ftpuploader backport to gpsftp4. Delay move from incoming.
- ftpuploader ported to gpsftp4 (CentOS 6). Do not try setting attributes of sftp upload.
- Delay move from $INCOMING to saved by 1s to allow remote client to set attributes on uploaded file.
- Delay removal of job from $JOBQUEUE by 1s to allow clients to change attributes after close.
- Do proper redirect of STDOUT and STDERR when commands are runned not using shell.
- Use external uncompressers as perl Gzip/Zip packages are not thread safe.
- Be more patient waiting for multi-file uploads to complete.

## 2019-12-26	sjm	Optimize hour locking. Handle inbound while active.
- Unpack in subdirs and move in locked mode when done unpacking.
- Reject new files once dayjob has started.

## 2019-12-25	sjm	Handle late arrival of multi-file upload file
- Rewrite gpspickup pending logic.
- Resubmit if one or more files in a multi-file upload arrived after job has been submitted.
- Add shortname to location table. Do not guess 9-ch sitename from shortname anymore.

## 2019-12-24	sjm	gpspickup unpack locking
- Ensure exclusive access when unpacking
- Make locking methods of status.? part of Job object

## 2019-12-23	sjm	QC parameters
- Changed QC calculation parameters to match EUREF 2018 recommendations.
  See http://epncb.oma.be/_documentation/guidelines/guidelines_analysis_centres.pdf

## 2019-12-22	sjm	Regorganize $SAVEDIR
- Inbound files are now saved in $SAVEDIR/$site/$year/$doy.
- Implement reprocess entire DOY. Only works if files are present in $SAVEDIR.
- Issue warning on Leica split hour files. Do nothing on splitted hours.

## 2019-12-16	sjm	Removed StatusDB.pm
- Removed StatusDB.pm again. Use individual horly status files and status.0 in exclusive mode.
- jobengine: Read all jobfiles and enqueue internally before running jobs.
- jobengine: Use Parallel::Fork::BossWorkerAsync instead of Parallel::ForkManager. It is much faster,
  offers automatic reaping and most importantly, non-blocking enqueuing of jobs to do.
- gpspickup: Use select() instead of alarm() in main loop.

## 2019-12-15	sjm	Use threads in gpspickup
- Make gpspickup multi-threaded to parallelize the unpacking process
- Increased the wait time for multi-file incoming file sets.
- Improve RinexSet->checkfiles
- Job can now be instantiated using a RinexSet as argument
- Use select() instead of alarm() for timeout on Inotify poll

## 2019-12-14	sjm	Move DOY complete check to Job.pm
- Moved check of DOY complete to Job.pm to make it independent of process reaping interval.
- Handle sites with no antannes or receivers defined.
- Fix bug processing inbound daily files.

## 2019-12-11	sjm	Check if doy already processed on all inbound data.
- Renamed check_existing to dailysum_exists. Check only if day is complete.
- Added dailysum_check on Septentrio files as well.

## 2019-12-10	sjm	Added StatusDB.pm
- StatusDB package to handle manipulation of workdir/status.json file in exclusive mode.

## 2019-12-06	sjm	Enable sumfile in DB and access via status.cgi
- Enable load of gzipped sumfile into DB and view of sumfile in QC (click on sumfield)

## 2019-12-05	sjm	Use G-Nut/Anubis instead of BNC for QC
- G-Nut/Anubis is much faster compared to BNC and the QC is more like TEQC QC
- Use gfzrnx to do the decimate from 1s to 30s. It is much faster than BNC.

## 2019-12-03	sjm	Config parameter jobinstances added.
- Add config parameter jobinstances. Default is 4 instances.
- Optimized scan loop in gpspickup and jobengine.
- Add -c file.conf option to gpspickup, jobengine and ftpuploader to specify alternate configuration.

## 2019-12-02	sjm	Location position and marker number may be unspecified.
- If not specifying the location position, the APPROX POSITION header will be left untouched.
- If markernumber is null, set to site if Unknown else leave original.
  If markernumber is set, always redefine markernumber in file.

## 2019-12-01	sjm	Admin.cgi, commands and signals
- Jobengine now able to perform custom commands via queue
- Admin.cgi tells jobengine when to reload ftpuploader or force completion of a day.
- Admin.cgi refuses to enable a ftpuploader rule if directory does not exist
- Improved signal handling in ftpuploader
- Make it possible to override default global contants (BaseConfig.pm) using /usr/local/etc/gorm.conf
- Add edit of antennas and receivers to admin.cgi

## 2019-11-30	sjm	Added partial port of admin.cgi
- admin.cgi handles create/edit sites, destinations, localdirs. Also forget DOY and finish imcompletes.

## 2019-11-29	sjm	Make ftpuploader compatible with gpsftp4
- Backport of ftpuploader to gpsftp4.

## 2019-11-29	sjm	Added support for Septentrio raw files
- Added support for Septentrio raw files (SBF format). This is now the preferred
  format from Septentrio receivers.

## 2019-11-27	sjm	gpspickup pending job logic updated.
- Make sure timer is updated for each new file in a multi file upload.
  Also Check that no files belonging to a pending job are being uploaded before submitting.

## 2019-11-26	sjm	Database scheda documentation added.
- Commenting and documentation.
- util/forget and util/loadsitelog utility added.

## 2019-11-25	sjm	Initial release.
- First release includes processing of Leica, Trimble and Septentrio RINEX3
  files. Processing is RINEX header rewrite, decimate 1s->30s, pack
  and distribute to recipients. Also includes QC status viewer (status.cgi).
