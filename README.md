# chandra_ciao

Note for Chandra data process:

抽谱：

http://cxc.harvard.edu/ciao/threads/pointlike/
http://cxc.harvard.edu/ciao/threads/extended/



从多个观测id里抽同一坐标的谱

download_chandra_obsid 441,582
punlearn chandra_repro
chandra_repro indir= 441,582 mode=h

punlearn reproject_obs
reproject_obs 441,582 reproj/


vi multi_src.lis 出来的是

reproj/441_reproj_evt.fits[sky=region(441_src.reg)]
reproj/582_reproj_evt.fits[sky=region(582_src.reg)]

其中 441_src.reg 是

# Region file format: CIAO version 1.0
circle(3:32:08.637,-27:47:35.50,0.184936')

这种格式的文本，其中reg文件介绍在： http://spiff.rit.edu/tass/ds9/region.html#Region%20File%20Format

用
circle 100 100 10 
circle(100 100 10) 
circle(100,100,10)
这种也行


mkdir spec
punlearn specextract
pset specextract infile=@multi_src.lis
pset specextract outroot=spec/441,spec/582
pset specextract bkgfile=@multi_bg.lis
specextract mode=h


punlearn combine_spectra
pset combine_spectra src_spectra=@src.lis
pset combine_spectra src_arfs=@src_arf.lis
pset combine_spectra src_rmfs=@src_rmf.lis
pset combine_spectra bkg_spectra=@bg.lis
pset combine_spectra bkg_arfs=@bg_arf.lis
pset combine_spectra bkg_rmfs=@bg_rmf.lis
combine_spectra


其中 
unix% cat src.lis
10289.pi
10290.pi
10291.pi
10292.pi
10293.pi


叠谱 
http://cxc.harvard.edu/ciao/threads/coadding/
http://cxc.harvard.edu/ciao/ahelp/combine_spectra.html


ds9 reproj/441_reproj_evt.fits -bin filter 'energy=500:7000' \
  -bin about 2000 4150 -bin factor 2 -cmap b -region 441_src.reg -region 441_bg.reg \
  reproj/582_reproj_evt.fits -bin filter 'energy=500:7000' \
  -bin about 2000 4150 -bin factor 2 -cmap b -region 582_src.reg \
  -region 582_bg.reg -tile row




