Get data, put it here.

    teichman@capek:/dysnomia/driving_catkin_advanced/src/driving/projects/nyt_demo/workspace$ l unlabeled_tds/2013/
    total 22G
    -rw-rw---- 1 teichman teichman  986M Feb  4 17:22 ShortLoop0-TwoLoops_2013-05-08-15-59-54-partition01.td
    -rw-rw---- 1 teichman teichman  981M Feb  4 17:15 ShortLoop0-TwoLoops_2013-05-08-15-59-54-partition02.td
    -rw-rw---- 1 teichman teichman  836M Feb  4 17:19 ShortLoop0-TwoLoops_2013-05-08-15-59-54-partition03.td
    -rw-rw---- 1 teichman teichman  988M Feb  4 17:16 ShortLoop1-TwoLoops_2013-05-08-16-08-28-partition00.td
    -rw-rw---- 1 teichman teichman  987M Feb  4 17:10 ShortLoop1-TwoLoops_2013-05-08-16-08-28-partition01.td
    -rw-rw---- 1 teichman teichman  960M Feb  4 17:14 ShortLoop1-TwoLoops_2013-05-08-16-08-28-partition02.td
    -rw-rw---- 1 teichman teichman  986M Feb  4 17:21 ShortLoop2-TwoLoops_2013-05-08-16-15-06-partition00.td
    -rw-rw---- 1 teichman teichman  982M Feb  4 17:17 ShortLoop2-TwoLoops_2013-05-08-16-15-06-partition01.td
    -rw-rw---- 1 teichman teichman  996M Feb  4 17:25 ShortLoop2-TwoLoops_2013-05-08-16-15-06-partition02.td
    -rw-rw---- 1 teichman teichman  881M Feb  4 17:13 ShortLoop2-TwoLoops_2013-05-08-16-15-06-partition03.td
    -rw-rw---- 1 teichman teichman  210M Feb  4 17:11 ShortLoop2-TwoLoops_2013-05-08-16-15-06-partition04.td
    -rw-rw---- 1 teichman teichman  985M Feb  4 17:16 swedish_tv_visit_00_2013-06-13-17-28-52-partition01.td
    -rw-rw---- 1 teichman teichman  991M Feb  4 17:12 swedish_tv_visit_00_2013-06-13-17-28-52-partition02.td
    -rw-rw---- 1 teichman teichman  980M Feb  4 17:12 swedish_tv_visit_00_2013-06-13-17-28-52-partition03.td
    -rw-rw---- 1 teichman teichman  981M Feb  4 17:18 swedish_tv_visit_00_2013-06-13-17-28-52-partition04.td
    -rw-rw---- 1 teichman teichman  989M Feb  4 17:24 swedish_tv_visit_00_2013-06-13-17-28-52-partition05.td
    -rw-rw---- 1 teichman teichman  904M Feb  4 17:23 swedish_tv_visit_00_2013-06-13-17-28-52-partition06.td
    -rw-rw---- 1 teichman teichman  964M Feb  4 17:26 swedish_tv_visit_00_2013-06-13-17-28-52-partition07.td
    -rw-rw---- 1 teichman teichman  649M Feb  4 17:11 swedish_tv_visit_00_2013-06-13-17-28-52-partition08.td
    -rw-rw---- 1 teichman teichman  982M Feb  4 17:14 swedish_tv_visit_01_2013-06-13-17-45-14-partition00.td
    -rw-rw---- 1 teichman teichman  994M Feb  4 17:24 swedish_tv_visit_01_2013-06-13-17-45-14-partition02.td
    -rw-rw---- 1 teichman teichman 1016M Feb  4 17:26 swedish_tv_visit_01_2013-06-13-17-45-14-partition03.td
    -rw-rw---- 1 teichman teichman   79M Feb  4 17:13 swedish_tv_visit_01_2013-06-13-17-45-14-partition04.td
    -rw-rw---- 1 teichman teichman  638M Feb  4 17:19 swedish_tv_visit_02_2013-06-13-17-59-37-partition00.td
    -rw-rw---- 1 teichman teichman  724M Feb  4 17:10 swedish_tv_visit_02_2013-06-13-17-59-37-partition01.td


Run group induction on this data.

    teichman@capek:/dysnomia/driving_catkin_advanced/src/driving/projects/nyt_demo/workspace$ make new_object_class/induction 


There are two views. First, the active learning view shows tracks from unlabeled data at random. Keys:

    'n' - Select a new dataset path at random.
    'N' - Load that dataset.
    ' ' - Pause or unpause group induction.
    , and . - step through tracks one at a time.
    < and > - step through tracks 100 at a time.
    'a' - Cycle the label to apply.
    Shift-Return - Send the labeled track to group induction.
    Alt-? - Switch to the induction view.


Provide a few seed tracks here - one or two positive examples of the thing you are looking for and maybe 10-20 negative examples should be fine. Then make sure group induction is not paused and switch to the induction view.

The induction view shows inducted tracks. 'I' re-loads the inducted set for visualization. Let group induction run for a while, then press 'I' and look at the inducted tracks. It's a good idea to scan through the things it is confident about on either end, then focus on the things it is unsure about in the middle, near log odds zero. Provide some labels, let group induction run for a few iterations, press 'I', repeat.

When satisfied, C-c the process and copy the classifier.

    nyt_demo/workspace$ cp `find new_object_class/ -name classifier.gc | sort | tail -n1` classifier.gc


Edit the classifier.gc directly to change the class name to something else. It's a mixed text / binary file but should be obvious at the top. Search for new_object_class.

Merge classifiers.

    $ rosrun online_learning merge_classifiers 2014-02-04-cars.gc pedestrian.gc -o merged.gc

Give the classifier a sensible name and host somewhere, then add a line like this to the classification CMakeLists.txt.

    stdr_download_data(http://cs.stanford.edu/people/teichman/2013-06-12_classifier.gc FILENAME data/classifier.gc MD5 21ce608e5c5c8a3418b262b94c0e1f7b)

Now the classifier will be downloaded when you run this:

    $ catkin_make download_extra_data

To update which classifier to use when driving:

    $ rosed perception PerceptionParam.launch

