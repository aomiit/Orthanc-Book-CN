Orthanc版本控制
==================

Orthanc的每个版本都由三个部分组成的版本来标识: ``API.MAJOR.MINOR``
 (e.g. 0.5.1).

* API (现在是 1)重构REST API时更改.
* MAJOR改变：当一个新的主要特性引入的时候(如REST API，DICOM支持),当数据库模式中的不兼容性被引入的时候，或者重大的重构完成的时候。
* MINOR改变： 在每次引入一个小特性之后，在错误修复之后，或者在速度或GUI得到改进后。当引入一个实验特性时，它也会发生变化。
