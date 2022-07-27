<template>
    <!--
    根目录规范(必须不能为空)：
    idm-ctrl：控件类型 drag_container：容器，drag_container_inlieblock：行内容器，idm_module：非容器的组件
    id：使用moduleObject.id，如果id不使用这个将会被idm-ctrl-id属性替换
    idm-ctrl-id：组件的id，这个必须不能为空
  -->
    <div idm-ctrl="idm_module" :id="moduleObject.id" :idm-ctrl-id="moduleObject.id">
        <!-- 组件内部容器 增加class="drag_container" 必选 idm-ctrl-id：组件的id，这个必须不能为空 idm-container-index  组件的内部容器索引，不重复唯一且不变，必选 -->
        <!-- {{propData.meetingInfo}} -->
        <div class="idm_meetingInfo clearFix">
            <div class="idm_meetingInfo_left fl">
                <img src="../assets/img/userPhoto.png" alt="">
            </div>
            <div class="idm_meetingInfo_right fl">
                <div class="idm_meetingInfo_userName">
                    <span>{{propData.userName}}</span>
                    <i class="iconfont icon-erweima" v-show='propData.userInfoCode'></i>
                </div>
                <div class="idm_meetingInfo_meetName clearFix">
                    <span>{{propData.meetName}}</span>
                    <i class="iconfont icon-qiehuan1" v-show='propData.meetSwitch'></i>
                </div>
                <div class="idm_meetingInfo_meetDate">
                    <i class="iconfont icon-shijian" v-show='propData.isShowIcon'></i>
                    <span>{{propData.meetDate}}</span>
                </div>
                <div class="idm_meetingInfo_meetPlace">
                    <i class="iconfont icon-dingwei" v-show='propData.isShowIcon'></i>
                    <span>{{propData.meetPlace}}</span>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'IMeetingInfo',
    components: {

    },
    data () {
        return {
            moduleObject: {},
            propData: {},
        }
    },
    created () {
        this.moduleObject = this.$root.moduleObject;
        this.propData = this.$root.propData.compositeAttr || {
            userImgSize: 100,
            userName: '李四',
            userInfoCode: true,
            meetName: '会议室名称',
            meetSwitch: true,
            meetSwitchSize: 30,
            meetDate: '2022-07-01 10:00-12:00',
            meetPlace: '第五会议室 五排二座',
            isShowIcon: true,
            iconSize: 30
        }
        this.convertAttrToStyleObject();
    },
    mounted () {

    },
    destroyed () { },
    methods: {
        /**
        * 适配页面
        */
        translatePxToAdaptation (data) {
            const clientWidth = this.currentEquipWidth;
            if (!data || !clientWidth) {
                return data;
            }
            const adaptationBase = this.propData.adaptationBase || 414;
            const adaptationPercent = this.propData.adaptationPercent || 1;
            const percent = (clientWidth / adaptationBase - 1) * (adaptationPercent - 1) + 1;
            return data * percent;
        },
        /**
       * 提供父级组件调用的刷新prop数据组件
       */
        propDataWatchHandle (propData) {
            this.propData = propData.compositeAttr || {};
            this.convertThemeListAttrToStyleObject();
            this.convertAttrToStyleObject();
        },
        /**
         * 把属性转换成样式对象
         */
        convertAttrToStyleObject () {
            let styleObject = {};
            let iconSizeStyleObject = {};
            if (this.propData.bgSize && this.propData.bgSize == "custom") {
                styleObject["background-size"] = (this.propData.bgSizeWidth ? this.propData.bgSizeWidth.inputVal + this.propData.bgSizeWidth.selectVal : "auto") + " " +
                    (this.propData.bgSizeHeight ? this.propData.bgSizeHeight.inputVal + this.propData.bgSizeHeight.selectVal : "auto");
            } else if (this.propData.bgSize) {
                styleObject["background-size"] = this.propData.bgSize;
            }
            if (this.propData.positionX && this.propData.positionX.inputVal) {
                styleObject["background-position-x"] =
                    this.propData.positionX.inputVal + this.propData.positionX.selectVal;
            }

            if (this.propData.positionY && this.propData.positionY.inputVal) {
                styleObject["background-position-y"] =
                    this.propData.positionY.inputVal + this.propData.positionY.selectVal;
            }
            for (const key in this.propData) {
                if (this.propData.hasOwnProperty.call(this.propData, key)) {
                    const element = this.propData[key];
                    if (!element && element !== false && element != 0) {
                        continue;
                    }
                    switch (key) {
                        case "width":
                        case "height":
                            styleObject[key] = element;
                            break;
                        case "box":
                            if (element.marginTopVal) {
                                styleObject["margin-top"] = `${element.marginTopVal}`;
                            }
                            if (element.marginRightVal) {
                                styleObject["margin-right"] = `${element.marginRightVal}`;
                            }
                            if (element.marginBottomVal) {
                                styleObject["margin-bottom"] = `${element.marginBottomVal}`;
                            }
                            if (element.marginLeftVal) {
                                styleObject["margin-left"] = `${element.marginLeftVal}`;
                            }
                            if (element.paddingTopVal) {
                                styleObject["padding-top"] = `${element.paddingTopVal}`;
                            }
                            if (element.paddingRightVal) {
                                styleObject["padding-right"] = `${element.paddingRightVal}`;
                            }
                            if (element.paddingBottomVal) {
                                styleObject["padding-bottom"] = `${element.paddingBottomVal}`;
                            }
                            if (element.paddingLeftVal) {
                                styleObject["padding-left"] = `${element.paddingLeftVal}`;
                            }
                            break;
                        case "bgColor":
                            if (element && element.hex8) {
                                styleObject["background-color"] = IDM.hex8ToRgbaString(element.hex8);
                            }
                            break;
                        case "bgImgUrl":
                            styleObject[
                                "background-image"
                            ] = `url(${window.IDM.url.getWebPath(element)})`;
                            break;
                        case "positionX":
                            //背景横向偏移
                            styleObject["background-position-x"] = element;
                            break;
                        case "positionY":
                            //背景纵向偏移
                            styleObject["background-position-y"] = element;
                            break;
                        case "bgRepeat":
                            //平铺模式
                            styleObject["background-repeat"] = element;
                            break;
                        case "bgAttachment":
                            //背景模式
                            styleObject["background-attachment"] = element;
                            break;
                        case "font":
                            styleObject["font-family"] = element.fontFamily;
                            if (element.fontColors.hex8) {
                                styleObject["color"] = IDM.hex8ToRgbaString(element.fontColors.hex8);
                            }
                            styleObject["font-weight"] = element.fontWeight && element.fontWeight.split(" ")[0];
                            styleObject["font-style"] = element.fontStyle;
                            styleObject["font-size"] = element.fontSize + element.fontSizeUnit;
                            iconSizeStyleObject['font-size'] = (element.fontStyle + 4) + 'px';
                            break;
                    }
                }
            }

            window.IDM.setStyleToPageHead(this.moduleObject.id + ' .hover_button .idm_filed_svg_icon', iconSizeStyleObject);
            window.IDM.setStyleToPageHead('idm_popupWorkbench_popup' + ' .hover_button', btnStyleObject);
            window.IDM.setStyleToPageHead(
                'idm_popupWorkbench_popup' + ' .hover_button .idm_filed_svg_icon',
                iconSizeStyleObject
            );
            window.IDM.setStyleToPageHead(
                'idm_popupWorkbench_popup' + ' .van-cell.cell_selected .van-cell__title div',
                cellSelectedStyleObject
            );
            window.IDM.setStyleToPageHead('idm_popupWorkbench_popup', popupStyleObject);
            window.IDM.setStyleToPageHead('idm_popupWorkbench_popup' + ' .van-cell', cellStyleObject);
            window.IDM.setStyleToPageHead(
                'idm_popupWorkbench_popup' + ' .van-cell .van-cell__title div',
                titleStyleObject
            );
            window.IDM.setStyleToPageHead(
                'idm_popupWorkbench_popup' + ' .van-cell .van-radio .van-radio__label',
                radioStyleObject
            );
        },
        /**
         * 主题颜色
         */
        convertThemeListAttrToStyleObject () {
            const themeList = this.propData.themeList;
            if (!themeList) {
                return;
            }
            const themeNamePrefix =
                IDM.setting &&
                    IDM.setting.applications &&
                    IDM.setting.applications.themeNamePrefix
                    ? IDM.setting.applications.themeNamePrefix
                    : "idm-theme-";
            for (var i = 0; i < themeList.length; i++) {
                var item = themeList[i];

                let dateNumStyleObject = {
                    "background-color": item.mainColor ? IDM.hex8ToRgbaString(item.mainColor.hex8) : "",
                };
                let todayStyleObject = {
                    "color": item.mainColor ? IDM.hex8ToRgbaString(item.mainColor.hex8) : "",
                };
                let titleSvgStyleObject = {
                    "color": item.mainColor ? IDM.hex8ToRgbaString(item.mainColor.hex8) : "",
                };

                IDM.setStyleToPageHead(
                    "." +
                    themeNamePrefix +
                    item.key +
                    " #" +
                    (this.moduleObject.packageid || "module_demo") +
                    " .i-schedule-header-tit i-schedule-header-tit-icon",
                    titleSvgStyleObject
                );
                IDM.setStyleToPageHead(
                    "." +
                    themeNamePrefix +
                    item.key +
                    " #" +
                    (this.moduleObject.packageid || "module_demo") +
                    " .swiper-wrapper .swiper-slide li .date-num.active",
                    dateNumStyleObject
                );
                IDM.setStyleToPageHead(
                    "." +
                    themeNamePrefix +
                    item.key +
                    " #" +
                    (this.moduleObject.packageid || "module_demo") +
                    " .swiper-wrapper .swiper-slide li .today.active",
                    todayStyleObject
                );
                IDM.setStyleToPageHead(
                    "." +
                    themeNamePrefix +
                    item.key +
                    " #" +
                    (this.moduleObject.packageid || "module_demo") +
                    " .i-schedule-content-note-left p.active::before",
                    dateNumStyleObject
                );
            }
        },
    }
}
</script>
<style scoped lang="scss">
.idm_meetingInfo {
    width: 100%;
    height: 200px;
    background-image: url('../assets/img/bg.png');
    background-size: 100% 100%;
    color: #fff;
    padding: 20px 10px 15px 10px;
    &_left {
        margin-right: 20px;
        img {
            width: 90px;
            height: 90px;
        }
    }
    &_right {
        width: calc(100% - 110px);
        div {
            width: 100%;
            padding: 6px 0;
        }
    }
    &_userName {
        margin-top: 10px;
        font-size: 24px;
        .iconfont {
            font-size: 22px;
            margin-left: 20px;
        }
    }
    &_meetName {
        font-size: 18px;
        position: relative;
        .iconfont {
            position: absolute;
            right: 0;
            top: 50%;
            transform: translateY(-50%);
            font-size: 21px;
        }
    }
    &_meetDate,
    &_meetPlace {
        .iconfont {
            font-size: 18px;
        }
        span {
            font-size: 14px;
            margin-left: 10px;
        }
    }
    &_meetDate {
        .iconfont {
            color: #ffac00;
        }
    }
    &_meetPlace {
        .iconfont {
            color: #32c5ff;
        }
    }
}
</style>