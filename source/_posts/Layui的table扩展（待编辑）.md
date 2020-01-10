---
title: Layui的table扩展（待编辑）
date: 2018-11-22 下午
comments: true
toc: true
categories: "前端,js,经验" 
copyright: true
---
>公共方法，直接上代码：
```javascript
var Table = function (ops) {
    this.dataList = [];
    this.table = {};
    this.tid = ops.id;
}
var laytable;

function bindNum(_this) {
    var index = $(_this).parents('tr')[0].dataset.index;
    var tableData = laytable.cache[$($(_this).context).attr('data-tid')]
    var dfield = $(_this).attr('data-field')
    if($(_this).val()==''){
        layer.open({
            content: '数值列不能为空且不能包含字母！',
            offset: "c"
        });
        $(_this).val(0)
        tableData[index][dfield] = 0;
    }else {
        tableData[index][dfield] = $(_this).val();
    }
}

Table.prototype = {
    getRowItemById: function (id) {
        var _item = null;
        var list = this.dataList
        for (var i = 0; i < list.length; i++) {
            var item = list[i];
            item.id == id ? _item = item : ''
            break;
        }
        return _item;
    },
    getRows: function () {
        var arr = this.table.cache[this.tid];
        var len = arr.length;
        console.log(arr)
        for (var i = 0; i < len; i++) {
            arr[i].sindex = arr[i].LAY_TABLE_INDEX;
        }
        return arr;
    },
    deleteRowItem: function (id) {
        var list = this.dataList;
        for (var i = list.length - 1; i >= 0; i--) {
            if (list[i].id == id) {
                list.splice(i, 1);
            }
        }
        this.reloadTable();
    },
    updateRowItem: function (item, key, callback) {
        var id = item[key]; //item:ajax获取的json； key:用于自定义标示数据行的flag，唯一；
        var idkey = this.idkey;
        var tid = this.tid;
        var arr = this.dataList;

        for (var ii = 0; ii < arr.length; ii++) {
            var _arr = arr[ii];
            var index = ii;
            if (_arr[idkey] == id) {
                arr[index] = item;
                var $div = $("#" + tid).next()
                var $tr = $div.find('.layui-table-body table.layui-table').find('tr').eq(index);
                var $tds = $tr.find('td');
                for (var i = 0; i < $tds.length; i++) {
                    var $td = $tds.eq(i);
                    var $field = $td.attr('data-field');
                    var $itemValue = item[$field];
                    var $tdValue = $td.find('div').html();
                    if ($itemValue != $tdValue) {
                        $td.find('div').html($itemValue);
                    }
                }
                continue;
            }
        }
        if (callback) {
            callback(item)
        }
    },
    dbClickTableItem: function (callback) {
    },
    addRowItem: function (item) {
        var oldList = this.table.cache[this.tid];

        var len = oldList.length;
        this.dataList = [];
        for (var i = len; i >= 0; i--) {
            this.dataList.unshift(oldList[i]);
        }

        oldList.unshift(item);
        this.dataList.unshift(item);
        this.reloadTable();
        this.bindDate();
    },
    bindDate: function () {
        var _this = this;
        layui.use('laydate', function () {
            var laydate = layui.laydate;
            lay('.table-time').each(function () {
                laydate.render({
                    elem: this,
                    trigger: 'click',
                    showBottom:false,
                    done: function (value, date) {
                        var index = $(this.elem).parents('tr')[0].dataset.index;
                        var id = this.elem.attr('id');
                        id = id.substring(0,id.indexOf('-'))
                        var tableData = _this.table.cache[id];
                        var dfield = $(this.elem)[0].parentNode.parentNode.getAttribute('data-field');
                        tableData[index][dfield] = value;
                    }
                });
            });
        })
        layui.use('laydate', function () {
            var laydate = layui.laydate;
            lay('.table-year').each(function () {
                laydate.render({
                    elem: this,
                    type:'year',
                    trigger: 'click',
                    showBottom:false,
                    change: function (value, date, endDate) {
                        this.elem.val(value);
                        if ($(".layui-laydate").length) {
                            $(".layui-laydate").remove();
                        }
                        var index = $(this.elem).parents('tr')[0].dataset.index;
                        var id = this.elem.attr('id');
                        id = id.substring(0,id.indexOf('-'))
                        var tableData = _this.table.cache[id];
                        var dfield = $(this.elem)[0].parentNode.parentNode.getAttribute('data-field');
                        tableData[index][dfield] = value;
                    },
                    done: function (value, date) {
                    }
                });
            });
        })
    },
    deleteRows: function () {
        var _this = this;
        layer.confirm('确定删除所选行么', function (index) {
            var arr = _this.table.cache[_this.tid]
            var newArr = arr.filter(function (key) {
                return !key.LAY_CHECKED;
            })
            _this.dataList = newArr;
            _this.reloadTable();
            layer.msg('删除成功', {icon: 1});
            layer.close();
        });
    },
    downloadRows: function () {
        var _this = this;
        layer.confirm('确定删除所选行么', function (index) {
            var arr = _this.table.cache[_this.tid]
            var newArr = arr.filter(function (key) {
                return key.LAY_CHECKED;
            })
            for (var i = 0; i < newArr.length; i++) {
                downloadFile(newArr[i].id);
            }
        });
    },
    delectFileRows: function () {
        layer.confirm('确定删除所选行么', function (index) {
            var arr = _this.table.cache[_this.tid]
            var newArr = arr.filter(function (key) {
                return key.LAY_CHECKED;
            })
            for (var i = 0; i < newArr.length; i++) {
                (newArr[i].id);
            }
        });
        reomoveFile();
    },
    deleteRowsMember: function () {
        var _this = this;
        var arr = _this.table.cache[_this.tid]
        var newArr = arr.filter(function (key) {
            return !key.LAY_CHECKED;
        })
        _this.dataList = newArr;
        _this.reloadTable();
    },
    getRowDateId: function () {
        var _this = this;
        var arr = _this.table.cache[_this.tid]
        var newArr = arr.filter(function (key) {
            return key.LAY_CHECKED;
        });
        if (newArr.length > 1) {
            layer.msg('请选择单条数据！', {icon: 1});
            return;
        }
        return newArr[0].id;
    },
    getRowDateIds: function () {
        var _this = this;
        var arr = _this.table.cache[_this.tid]
        var newArr = arr.filter(function (key) {
            return key.LAY_CHECKED;
        });
        return newArr;
    },
    reloadTable: function (callback) {
        var list = this.dataList;
        this.table.reload(this.tid, {
            data: list
        });
        //重新渲染所有table
        for (var key in this.table.cache) {
            this.table.reload(key);
        }
        //console.log(this.table.cache)

        this.bindDate();
        typeof callback == 'function' ? callback() : '';
    },
    clearTable: function () {
        this.dataList = [];
        this.reloadTable();
    },
    renderTable: function (ops, callback) {
        var _this = this;

        if (ops.toolbars) {
            ops.toolbar = '#' + this.tid + "_toolbar";
            if (!ops.defaultToolbar) {
                ops.defaultToolbar = [];
            }
        }
        ops.elem = '#' + this.tid;
        if (ops.area) {
            ops.width = ops.area[0];
            ops.height = ops.area[1];
        } else {
            ops.width = 950;
            ops.height = 260;
        }

        if (ops.page == undefined) {
            ops.page = false;
            ops.limit = 100;
        }

        //添加下拉框
        if (ops.cols[0]) {
            for (var i = 0, len = ops.cols[0].length; i < len; i++) {
                if (ops.cols[0][i].templet == 'date') {
                    var dfield = ops.cols[0][i].field
                    ops.cols[0][i].type = 'date';
                    ops.cols[0][i].templet = function date(d) {//新增数据时才会渲染到这里，所以含有i的属性在这里不能起效
                        return '<input id="' + _this.tid + '-' + this.field + '" lay-filter="table-date" class="table-date layui-input table-time ' + _this.tid + '" data-value="' + d[this.field] + '"></input>';
                    }
                }

                if (ops.cols[0][i].templet == 'year') {
                    var dfield = ops.cols[0][i].field
                    ops.cols[0][i].type = 'year';
                    ops.cols[0][i].templet = function date(d) {//新增数据时才会渲染到这里，所以含有i的属性在这里不能起效
                        return '<input id="' + _this.tid + '-' + this.field + '" lay-filter="table-year" class="table-year layui-input ' + _this.tid + '" data-value="' + d[this.field] + '"></input>';
                    }
                }

                if (ops.cols[0][i].templet == 'number') {
                    var dfield = ops.cols[0][i].field
                    ops.cols[0][i].type = 'number';

                    ops.cols[0][i].templet = function date(d) {//新增数据时才会渲染到这里，所以含有i的属性在这里不能起效
                        return '<input onkeyup=\'value=value.replace(/[^A-Za-z0-9\\-]+/g,"")\' type="number" class="table-number layui-input ' + _this.tid + '" data-tid="' + _this.tid + '" data-field="' + this.field + '"  data-value="' + d[this.field] + '" onblur="bindNum(this);"></input>';
                    }
                }
                if(ops.cols[0][i].templet == 'numberPoint'){
                    var dfield = ops.cols[0][i].field
                    ops.cols[0][i].type = 'number';

                    ops.cols[0][i].templet = function date(d) {//新增数据时才会渲染到这里，所以含有i的属性在这里不能起效
                        return '<input onkeyup=\'num(this)\' type="number" class="table-number layui-input ' + _this.tid + '" data-tid="' + _this.tid + '" data-field="' + this.field + '"  data-value="' + d[this.field] + '" onblur="bindNum(this);"></input>';
                    }
                }

                if (ops.cols[0][i].templet && typeof(ops.cols[0][i].templet) != 'function' && ops.cols[0][i].templet.indexOf('DICT_') != -1) {

                    var DICT_TYPE = ops.cols[0][i].templet.replace("DICT_", "");
                    var dfield = ops.cols[0][i].field
                    ops.cols[0][i].type = 'dict';

                    $.ajax({
                        cache: true,
                        type: "GET",
                        url: "/common/dict/list/" + DICT_TYPE,
                        async: false,
                        error: function (request) {
                            layer.msg("连接错误");
                        },
                        success: function (data) {
                            var options = "";
                            for (var i = 0, len = data.length; i < len; i++) {
                                options += '        <option value="' + data[i].value + '">' + data[i].name + '</option>\n'
                            }
                            setTemplet(options, dfield)
                        }
                    });

                    function setTemplet(options, field) {
                        ops.cols[0][i].templet = function dict(d) {
                            return '<select lay-filter="table-dict" class="table-select" lay-verify="required" datafield="' + field + '" data-value="' + d[field] + '" >\n' +
                                '        <option value=""></option>\n' +
                                options +
                                '</select>';
                        }
                    }
                }
            }
            ops.done = function (res, curr, count) {
                count || this.elem.next('.layui-table-view').find('.layui-table-header').css('overflow', 'auto');
                layui.each($('select'), function (index, item) {
                    var elem = $(item);
                    if (elem[0].classList.contains('table-select')) {
                        elem.val(elem.data('value')).parents('div.layui-table-cell').css('overflow', 'visible');
                    }
                });
                layui.each($('input'), function (index, item) {
                    var elem = $(item);
                    if ((elem[0].classList.contains('table-date')||elem[0].classList.contains('table-number')||elem[0].classList.contains('table-year')) && elem[0].classList.contains(_this.tid)) {
                        elem.val(elem.data('value')).parents('div.layui-table-cell').css('overflow', 'hidden');
                    }
                });
                ops.form.render();
            }
        }

        this.table.render(ops);

        //可编辑
        if (ops.editFun) {
            this.table.on('edit(' + this.tid + ')', function (obj) {
                typeof ops.editFun == 'function' ? ops.editFun(obj) : '';
            })
        }
        //工具栏
        if (ops.toolbars) {
            this.bindToolbar(ops);
        }

        //行内编辑
        if (ops.tools) {
            this.bindTool(ops)
        }
        if (ops.form) {
            this.bindTemplet(ops, ops.form)
        }

        _this.ops = ops;
        _this.dataList = ops.data;
        this.reloadTable();

        //表头提示
        $("thead tr th div").each(function () {
            $(this)[0].title = $(this)[0].innerText;
        });

        typeof callback == 'function' ? callback() : '';
    },
    initialize: function (callback) {
        var _ = this;
        layui.use('table', function () {
            laytable = layui.table;
            _.table = laytable;
            typeof callback == 'function' ? callback(_.table) : '';
        })
    },

    bindToolbar: function (ops) {
        var toolbarDom = "<script type=\"text/html\" id=" + this.tid + "_toolbar" + ">\n" +
            "    <div class=\"layui-btn-container\">\n";
        for (var i = 0, len = ops.toolbars.length; i < len; i++) {
            if (ops.toolbarColors) {
                toolbarDom += "<button type='button' class='layui-btn layui-btn-sm " + ops.toolbarColors[i] + "' lay-event='" + ops.toolbars[i] + "'>" + ops.toolbarNames[i] + "</button>\n";
            } else {
                toolbarDom += "<button type='button' class='layui-btn layui-btn-sm' lay-event='" + ops.toolbars[i] + "'>" + ops.toolbarNames[i] + "</button>\n";
            }
        }
        toolbarDom += "    </div>\n";
        toolbarDom += "</script>";
        $("body").append(toolbarDom);
        this.table.on('toolbar(' + this.tid + ')', function (obj) {
            typeof ops.toolbarFun == 'function' ? ops.toolbarFun(obj) : '';
        })
    },
    bindTool: function (ops) {
        var toolDom = "<script type=\"text/html\" id=" + this.tid + "_tool" + ">\n";
        for (var i = 0, len = ops.tools.length; i < len; i++) {
            toolDom += "<button type='button' class='layui-btn layui-btn-xs ";
            if (ops.toolColors) {
                toolDom += ops.toolColors[i];
            } else {
                toolDom += 'layui-btn-normal';
            }
            toolDom += "' lay-event='" + ops.tools[i] + "'>" + ops.toolNames[i] + "</button>\n";
        }
        toolDom += "</script>";
        $("body").append(toolDom);
        this.table.on('tool(' + this.tid + ')', function (obj) {
            typeof ops.toolFun == 'function' ? ops.toolFun(obj) : '';
        })
    },
    bindTemplet: function (ops, form) {
        var _this = this;
        // 监听修改update到表格中

        form.on('select(table-dict)', function (data) {
            var elem = $(data.elem);
            var tableId = $(elem.parents('tr').context.parentNode.parentNode.parentNode.parentNode.parentNode.parentNode.parentNode.parentNode).attr('lay-id');
            var tableData = _this.table.cache[tableId];
            var tableField = $($(this).context.parentNode.parentNode.parentNode.firstChild).attr('datafield');
            var index = $(elem.parents('tr')).data('index');
            // 更新到表格的缓存数据中，才能在获得选中行等等其他的方法中得到更新之后的值
            tableData[index][tableField] = data.value;
            // 其他的操作看需求 TODO
        });
    }
}


```
>不积跬步，无以至千里