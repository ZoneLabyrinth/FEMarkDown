### awesome-typescript-loader

使用`atl`按需引入,不能使用 `checkplugin`

```js
{
                test: /\.(js|jsx|ts|tsx)$/,
                exclude: /node_modules/,
                use: [
                    //在thread-loader前
                    {
                        loader: 'awesome-typescript-loader',
                        options: {
                            getCustomTransformers: () => ({
                                before: [
                                    tsImportPluginFactory({ libraryName: 'antd-mobile', style: 'css' }),
                                ]
                            }),
                        },
                    },
                ]
}
```





reactnode,

reacrtelement

elementtype