# webpack笔记







## 优化

### 体积优化

#### 分析

通过使用webpack-bundle-analyzer可以看到项目各模块的大小，可以按需优化。

1. 安装

   ```sh
   npm install --save-dev webpack-bundle-analyzer
   ```

2. 在webpack.config.js中配置

   ```js
   const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
   
   plugins: [
   	new BundleAnalyzerPlugin(
       	{
                 analyzerMode: 'server',
                 analyzerHost: '127.0.0.1',
                 analyzerPort: 8889,
                 reportFilename: 'report.html',
                 defaultSizes: 'parsed',
                 openAnalyzer: true,
                 generateStatsFile: false,
                 statsFilename: 'stats.json',
                 statsOptions: null,
                 logLevel: 'info'
       	}
   	),
   ]
   ```

3. 在package.json的scripts里加入下面这句话，就可以npm run build之后看到webpack-bundle-analyzer的效果

   ```json
   "analyz": "NODE_ENV=production npm_config_report=true npm run build"
   ```
