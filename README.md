# EasyCaching.Extensions

[EasyCaching](https://github.com/dotnetcore/EasyCaching)项目的第三方扩展：[Autofac](https://github.com/autofac/Autofac)、[WebApiClient](https://github.com/dotnetcore/WebApiClient)、[CAP](https://github.com/dotnetcore/CAP)、[Microsoft.Extensions.Caching IDistributedCache](https://github.com/aspnet/Extensions/tree/master/src/Caching)

### 1 CAP扩展
#### 1.1 Nuget
```
PM> Install-Package YrinLeung.EasyCaching.Bus.CAP
```
支持 netstandard2.0

#### 1.2 使用方法

> Startup相关配置

```c#

public void ConfigureServices(IServiceCollection services)
{
	
	//use EasyCaching
	services.AddEasyCaching(option =>
	{
		// local
		option.UseInMemory("m1");
		// distributed
		option.UseRedis(config =>
		{
			config.DBConfig.Endpoints.Add(new Core.Configurations.ServerEndPoint("127.0.0.1", 6379));
			config.DBConfig.Database = 5;
		}, "myredis");

		// combine local and distributed
		option.UseHybrid(config =>
		{
			config.TopicName = "test-topic";
			config.EnableLogging = false;
			
			config.LocalCacheProviderName = "m1";
			config.DistributedCacheProviderName = "myredis";
		});
		// use CAP bus
		option.WithCapBus(x =>
		{
			x.TopicName = "test-topic";
		});

	});
	//use CAP ，根据CAP官方文档配置即可
	services.AddCap(x =>
	{
		x.UseInMemoryStorage();
		x.UseRabbitMQ(configure =>
		{
			configure.HostName = "127.0.0.1";
			configure.UserName = "admin";
			configure.Password = "admin";
		});
		x.UseDashboard();
	});
	...
}

```

#### 1.3 Demo
查看 [Demo](https://github.com/yrinleung/EasyCaching.Extensions/tree/master/samples)

### 2 WebApiClient扩展
#### 2.1 Nuget
```
PM> Install-Package YrinLeung.EasyCaching.Interceptor.WebApiClient
```
支持 netstandard2.0

#### 2.2 使用方法

> Startup相关配置

```c#

public void ConfigureServices(IServiceCollection services)
{
	//将WebClient接口注入
    services.AddWebApiClientUseHttpClientFactory<IWebApiClientService>();
	//or
	// services.AddWebApiClientUseHttpClientFactory<IWebApiClientService>((httpApiConfig) =>
	// {
	// 	httpApiConfig.HttpHost = new Uri("http://www.baidu.com");
	// });
	//or
	// services.AddWebApiClientUseHttpClientFactory<IWebApiClientService>((httpApiConfig, p) =>
	// {
	// 	httpApiConfig.HttpHost = new Uri("http://www.baidu.com");
	// });
	...
}

```

#### 2.3 Demo
查看 [Demo](https://github.com/yrinleung/EasyCaching.Extensions/tree/master/samples/EasyCaching.Extensions.Demo)


### 3 AspectCore的Autofac扩展
#### 3.1 Nuget
```
PM> Install-Package YrinLeung.EasyCaching.Interceptor.AspectCore.Autofac
```
支持 netstandard2.0

#### 3.2 使用方法

> Startup相关配置

```c#

public void ConfigureContainer(ContainerBuilder builder)
{
	//将AspectCore加入Autofac
	builder.AddAspectCoreInterceptor(x => x.CacheProviderName = EasyCachingConstValue.DefaultInMemoryName);
}

```

#### 3.3 Demo
查看 [Demo](https://github.com/yrinleung/EasyCaching.Extensions/tree/master/samples/EasyCaching.Extensions.Demo)

### 4 Castle的Autofac扩展
#### 4.1 Nuget
```
PM> Install-Package YrinLeung.EasyCaching.Interceptor.Castle.Autofac
```
支持 netstandard2.0

#### 4.2 使用方法

> Startup相关配置

```c#

public void ConfigureContainer(ContainerBuilder builder)
{
	//将Castle加入Autofac
	builder.AddCastleInterceptor(x => x.CacheProviderName = EasyCachingConstValue.DefaultInMemoryName);
}

```


#### 4.3 Demo
查看 [Demo](https://github.com/yrinleung/EasyCaching.Extensions/tree/master/samples/EasyCaching.Extensions.Demo)


### 5 Microsoft.Extensions.Caching.Distributed.IDistributedCache使用EasyCaching实现
#### 5.1 Nuget
```
PM> Install-Package YrinLeung.Microsoft.Extensions.Caching.EasyCaching
```
支持 netstandard2.0

#### 5.2 使用方法

> Startup相关配置

```c#

public void ConfigureServices(IServiceCollection services)
{
	services.AddEasyCachingCache(config =>
	{
		//EasyCaching的CachingProvider的Name
		config.CachingProviderName = "DefaultRedis";
		//默认滑动过期时间,默认20分钟
		config.DefaultSlidingExpiration = TimeSpan.FromMinutes(20);
	});
}

```


#### 5.3 Demo
查看 [Demo](https://github.com/yrinleung/EasyCaching.Extensions/tree/master/samples/EasyCaching.Extensions.Demo)
