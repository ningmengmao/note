# JPA

> #### 主键生成策略
>
> > GenerationType.IDENTITY  mysql
> >
> > GenerationType.SEQUENCE   oracle
> >
> > GenerationType.TABLE    
> >
> > GenerationType.AUTO
>
> #### 接口
>
> > * JpaRepostitory					   简单CURD
> >
> > * JpaSpecificationExcecutor   复杂查询
> >
> >   > ![image-20191121205359000](/home/ningmeng/文档/java/img/image-20191121205359000.png)
> >
> > ctrl+f12 类的全部方法
> >
>
> #### 方法
>
> > findOne()    立即加载
> >
> > > em.find()
> >
> > getOne()      延迟加载
> >
> > > em.getReference()
> > >
> > > 返回的是动态代理对象
> > >
> > > 使用对象的时候才调用sql
>
> #### 复杂查询
>
> > ＠Query
> >
> > @Modifing + @Query 为更新操作
> >
> > > 更新/删除操作需要添加@Transactional, 默认会在执行完成后回滚
> > >
> > > @Rollbcak 是否自动回滚
>
> #### 动态查询
>
> >JpaSpecificationExecutor接口
> >
> >Specification<T>
>
> #### 多表关系维护
>
> > 



