# H5py python操作HDF5文件
## 文件对象
> 安装h5py
```
&pip install h5py
```
> 打开&创建文件
```
>>> import h5py
>>> f = h5py.File('myfile.hdf5','r') # 三种模式  r w a
```
> 指定文件驱动
```
# None 标准的HDF5驱动；sec2 无缓存，使用标准的POSIX函数优化IO
# core 存储操作数据在内存中；family 存储数据在磁盘盘上作为一连串的固定长度的块 fileobj 存储文件为类python文件
f = h5py.File('myfile.hdf5', drive='drive_name, drive_kwds') 
```
> python file-like对象
```
>>> import tempfile
>>> tf = tempfile.TemporaryFile()
>>> f = h5py.File(tf)
```
```
# io.BytesIO
>>> import io
>>> f = h5py.File(io.BytesIO())
>>> f['dataset'] = range(10)
>>> data = bio.getvalue()
```
> 指定HDF5 版本
```
>>> f = h5py.File('myfile.hdf5', libver=('earliest', 'v108'))
```
> 用户块
```
# 用户自定义文件前的保留空间，默认为0，通过userblock_size设定大小，一经设定不可更改
>>> f = h5py.File('myfile.hdf5', userblock_size=512)
```
> 块 cache
```
# rdcc_nbytes 设置每个数据集的缓存大小
# rdcc_w0 置换模式
# 缓存中块槽数，此值应至少是可以适合rdcc_nbytes个字节的块数的10倍。为获得最佳性能，此值应设置为块数约100倍。默认值为521。
```
## 组对象
> root group
```
>>> f = h5py.file('foo.hdf5','w')
>>> f.name
>>> f.keys()
```
> 创建组
```
>>> group = f.create_group('bar')
>>> group.name
>>> sub_group = group.create_group('baz')
>>> sub_group.name
> 创建多个组
>>> group2 = f.create_group('/some/long/path')
>>> group2.name
>>> group3 = f['/some/long']
>>> group2.name
```
> 查找组下对应的数据集
```
>>> mds = sub_group['MyDS']
>>> del sub_group['MyDS']
>>> keys = sub_group.keys()
>>> values = sub_group.values()
```
> 硬连接
```
>>> group['name'] = 42
>>> out = group['name']
>>> group['other_name'] = out
>>> f['other_name'] = f['name']
```
> 软连接
```
group = f.create_group('somegroup')
f['alias'] = h5py.SoftLink('/somegroup')
```
> 外连接
```
>>> f['ext_link'] = h5py.ExternalLink('otherfile.hdf5', '/path/to/resorce')
```
## 数据集对象
数据集对象支持许多特透明存储的特性，例如压缩、容错、分块IO
> 创建数据集
```
>>> dset = f.create_dataset('default',(100,))
>>> dset = f.create_dataset('ints',(100,),dtype='i8')
>>> dset = f.create_dataset('init', data=np.arange(100))
```
> 分块存储
```
# HDF5默认的设置是连续存储，数据集使用HDF5的分块存储方式，即将数据集分成规则大小的块，并且使用B树索引相应的块。
>>> dset = f.create_dataset('chuked', (1000, 1000), chunks=(100,100))
>>> dset = f.create_dataset('chuked', (1000, 1000), chunks=True)
```
> 调整数据集大小
```
>>> dset = f.create_dataset('resizeble', (10,10), maxshape=(500,20))
>>> dset = f.create_dataset('unlimited', (10,10), maxshape=(None,10))
```
> Filter pipeline
```
# 过滤器管道的作用主要用于存储时压缩数据，读取时自动解压数据
# 压缩格式 gzip lzf szip 
# 压缩水平 0-9 默认为4
# shuffle = True 提高压缩率
# fletcher32 = True 容错检验
>>> dset = f.create_dataset('zipped', (100, 100), compression='gzip', compression_opts=9)
```
## 属性对象
属性对象是HDF5官方存储元数据的方方法，每个组、数据集都可以有属性对象，
> 属性对象操作
```
>>> attribute = group.attrs
>>> keys = attribute.keys()
>>> values = attribute.values()
>>> (keys,values) = attribute.items()
>>> value = attribute.get('key')
>>> new_attr = attribute.create('name',data,shape,dtype)
>>> attribute.modify('key',value)
```
## 特殊数据类型
> 创建变长数组
```
>>> dt = h5py.string_dtype(encoding='utf-8')
>>> ds = f.create_dataset('VLDS', (100,100), dtype=dt)
```
> 任意变长数据
```
>>> dt = h5py.vlen_dtype(np.dtype('int32'))
>>> ds = f.create_dataset('vlen_int', (100,), dtype=dt)
```
> 枚举类型
```
>>> dt = h5py.enum_dtype({"RED": 0, "GREEN": 1, "BLUE": 42}, basetype='i')
>>> ds = f.create_dataset("EnumDS", (100,100), dtype=dt)
```
## 对象和区域引用
对象区域引用和软连接以及外部连接相似。
> 使用对象引用
```
>>> group = f['/some/group']
>>> ref = group.ref
>>> group2 = f[ref]
```
> 使用区域引用
```
>>> ds = f.create_dataset('dset',(100,100))
>>> regref = ds.regionref[0:10,0:5]
>>> subset = ds[ref]
```
> 创建引用数据集
```
>>> ref_dataset = f.create_dataset('myreds', (100,), dtype=h5py.ref_dtype)
>>> ref_dataset[0] = f.ref
```
> 存储引用属性
```
>>> ref = f.ref
>>> f.attrs['root_group_ref'] = ref
```
## 并行
HDF5的并行基于MPI,本节简要介绍 MPI中相应的进程通讯方式，具体的PHDF5并行后续添加。
> MPI中的通讯方式
```
from mpi4py import MPI
import numpy as np
comm = MPI.COMM_WORLD
size = comm.Get_size()
rank = comm.Get_rank()
print('rank={}, size={}'.format(rank, size))
# 点对点发送
if rank == 0:
    data = {"a":7, "b":3.14}
    comm.send(data, dest = 1, tag = 11)
    print('send data = ', data)
    
elif rank == 1:
    data = comm.recv(source = 0, tag = 11)
    print('recv data = ', data)

# 点对点发送 非阻塞通讯
if rank == 0:
    data = {"a":7, "b":3.14}
    req = comm.isend(data, dest = 1, tag = 11)
    req.wait()
    print('isend data = ', data)
    
elif rank == 1:
    req = comm.irecv(source = 0, tag = 11)
    data = req.wait()
    print('irecv data = ', data)

# 发送Numpy数组
if rank == 0:
    data = np.arange(10,dtype = np.int)
    comm.Send(data, dest = 1,tag = 13)
    print("numpysend data = ",data)
elif rank == 1:
    data = np.empty(10,dtype = np.int)
    comm.Recv(data, source = 0,tag = 13)
    print("numpyrecv data = ",data)
    
# 群体通讯bcast
if rank == 0:
    data = {"key1":[7,2.72,2+3j],"key2":("abc","xyz")}
else:
    data = None
data = comm.bcast(data,root=0)
print('rank = ',rank,' ,data = ',data)

# 撒播Scattering
if rank == 0:
    data = [(i+1)**2 for i in range(size)]
else:
    data = None
data = comm.scatter(data,root=0)
assert data == (rank+1)**2
print('rank = ',rank,'data=',data)

# 汇集Gathering
data = (rank + 1) ** 2
data = comm.gather(data,root = 0)
if rank ==0:
    for i in range(size):
        assert data[i] == (i+1) ** 2
    print('gather data=',data)
else:
    assert data is None
```
## SWMR
Single Writer Multiple Reader(SWMR)主要用于多进程的并发控制，替代了锁和异步机制。
```
import sys, time
import h5py
import numpy as np
import logging
from multiprocessing import Process, Event

class SwmrReader(Process):
    def __init__(self, event, fname, dsetname, timeout=20.):
        super(SwmrReader, self).__init__()
        self._event = event
        self._fname = fname
        self._dsetname = dsetname
        self._timeout = timeout
        
    def run(self):
        self.log = logging.getLogger('reader')
        self.log.info("Waiting for initial event")
        assert self._event.wait(self._timeout)
        self._event.clear()
        
        self.log.info("open file %s", self._fname)
        f = h5py.File(self._fname, 'r', libver='latest', swmr=True)
        assert f.swmr_mode
        dset = f[self._dsetname]
        try:
            while self._event.wait(self._timeout):
                self._event.clear()
                self.log.debug("Refreshing dataset")
                dset.refresh()
                shape = dset.shape
                self.log.info("Read dset shape: %s" %str(shape))
        finally:
            f.close()
class SwmrWriter(Process):
    def __init__(self, event, fname, dsetname):
        super(SwmrWriter,self).__init__()
        self._event = event
        self._fname = fname
        self._dsetname = dsetname
        
    def run(self):
        self.log = logging.getLogger('writer')
        self.log.info("create file %s", self._fname)
        f = h5py.File(self._fname, 'w', libver='latest')
        try:
            arr = np.array([1,2,3,4])
            dset = f.create_dataset(self._dsetname, chunks=(2,), maxshape=(None,), data=arr)
            assert not f.swmr_mode
            self.log.info("SWMR mode")
            f.swmr_mode = True
            assert f.swmr_mode
            self.log.debug("Sending initail event")
            self._event.set()
            
            # Write loop
            for i in range(5000):
                new_shape = ((i+1) * len(arr),)
                self.log.info("resizing dset shape: %s" %str(new_shape))
                dset.resize(new_shape)
                self.log.debug("writing data")
                dset[i*len(arr):]=arr
                self.log.debug("Fushing data")
                dset.flush()
                self.log.info("Sending event")
                self._event.set()
        finally:
            f.close()
        
if __name__ == "__main__":
    logging.basicConfig(format='%(levelname)10s  %(asctime)s   %(name)10s  %(message)s',level=logging.INFO)
    fname = 'swmrmp.h5'
    dsetname = 'data'
    if len(sys.argv) > 1:
        fname = sys.argv[1]
    if len(sys.argv) > 2:
        dsetname = sys.argv[2]
    event = Event()
    reader = SwmrReader(event, fname, dsetname)
    writer = SwmrWriter(event, fname, dsetname)
    
    logging.info("starting reader")
    reader.start()
    logging.info("starting reader")
    writer.start()
    
    logging.info("Waiting for writer to finish")
    writer.join()
    logging.info("Waiting for reader to finish")
    reader.join()
```
## 参考资料
[H5PY](http://docs.h5py.org/en/latest/index.html)









