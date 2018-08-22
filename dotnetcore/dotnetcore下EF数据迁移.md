# dotnetcore下EF数据迁移
  
## dotnetcore 2.x Main() 入口修改

        /// </summary>
        public class Program
        {
            /// <summary>
            ///
            /// </summary>
            /// <param name="args"></param>
            public static void Main(string[] args)
            {
                BuildWebHost(args).Run();
            }

            /// <summary>
            /// BuildWebHost
            /// </summary>
            /// <param name="args"></param>
            /// <returns></returns>
            public static IWebHost BuildWebHost(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                        .UseContentRoot(Directory.GetCurrentDirectory())
                        .UseStartup<Startup>()
                        .UseUrls(new UriBuilder("http", "+", 51620).ToString())
                        .Build();
        }

## 使用Nuget进行安装EF Tool

    Microsoft.EntityFrameworkCore.Tools

## 迁移命令

    PM> Add-Migration init
        To undo this action, use Remove-Migration.
    PM> Update-Database init
        Applying migration '20170917062429_init'.
        Done.
