<template>
	<div class="right-board">
		<el-tabs v-model="currentTab" class="center-tabs">
			<el-tab-pane label="组件属性" name="field" />
			<el-tab-pane label="表单属性" name="form" />
		</el-tabs>
		<div class="field-box">
			<a
				class="document-link"
				target="_blank"
				:href="documentLink"
				title="查看组件文档"
			>
				<i class="el-icon-link" />
			</a>
			<el-scrollbar class="right-scrollbar">
				<!-- 组件属性 -->
				<el-form
					v-show="currentTab === 'field' && showField"
					size="small"
					label-width="90px"
				>
					<el-form-item
						v-if="activeDataTemp.__config__.changeTag"
						label="组件类型"
					>
						<el-select
							v-model="activeDataTemp.__config__.tagIcon"
							placeholder="请选择组件类型"
							:style="{ width: '100%' }"
							@change="tagChange"
						>
							<el-option-group
								v-for="group in tagList"
								:key="group.label"
								:label="group.label"
							>
								<el-option
									v-for="item in group.options"
									:key="item.__config__.label"
									:label="item.__config__.label"
									:value="item.__config__.tagIcon"
								>
									<svg-icon
										class="node-icon"
										:icon-class="item.__config__.tagIcon"
									/>
									<span> {{ item.__config__.label }}</span>
								</el-option>
							</el-option-group>
						</el-select>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__vModel__ !== undefined"
						label="字段名"
					>
						<el-input
							v-model="activeDataTemp.__vModel__"
							placeholder="请输入字段名（v-model）"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.componentName !==
							undefined
						"
						label="组件名"
					>
						{{ activeDataTemp.__config__.componentName }}
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.label !== undefined"
						label="标题"
					>
						<el-input
							v-model="activeDataTemp.__config__.label"
							placeholder="请输入标题"
							@input="changeRenderKey"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.placeholder !== undefined"
						label="占位提示"
					>
						<el-input
							v-model="activeDataTemp.placeholder"
							placeholder="请输入占位提示"
							@input="changeRenderKey"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['start-placeholder'] !== undefined"
						label="开始占位"
					>
						<el-input
							v-model="activeDataTemp['start-placeholder']"
							placeholder="请输入占位提示"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['end-placeholder'] !== undefined"
						label="结束占位"
					>
						<el-input
							v-model="activeDataTemp['end-placeholder']"
							placeholder="请输入占位提示"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.span !== undefined"
						label="表单栅格"
					>
						<el-slider
							v-model="activeDataTemp.__config__.span"
							:max="24"
							:min="1"
							:marks="{ 12: '' }"
							@change="spanChange"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.layout ===
								'rowFormItem' &&
							activeDataTemp.gutter !== undefined
						"
						label="栅格间隔"
					>
						<el-input-number
							v-model="activeDataTemp.gutter"
							:min="0"
							placeholder="栅格间隔"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.layout ===
								'rowFormItem' &&
							activeDataTemp.type !== undefined
						"
						label="布局模式"
					>
						<el-radio-group v-model="activeDataTemp.type">
							<el-radio-button label="default" />
							<el-radio-button label="flex" />
						</el-radio-group>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.justify !== undefined &&
							activeDataTemp.type === 'flex'
						"
						label="水平排列"
					>
						<el-select
							v-model="activeDataTemp.justify"
							placeholder="请选择水平排列"
							:style="{ width: '100%' }"
						>
							<el-option
								v-for="(item, index) in justifyOptions"
								:key="index"
								:label="item.label"
								:value="item.value"
							/>
						</el-select>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.align !== undefined &&
							activeDataTemp.type === 'flex'
						"
						label="垂直排列"
					>
						<el-radio-group v-model="activeDataTemp.align">
							<el-radio-button label="top" />
							<el-radio-button label="middle" />
							<el-radio-button label="bottom" />
						</el-radio-group>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.labelWidth !== undefined
						"
						label="标签宽度"
					>
						<el-input
							v-model.number="
								activeDataTemp.__config__.labelWidth
							"
							type="number"
							placeholder="请输入标签宽度"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.style &&
							activeDataTemp.style.width !== undefined
						"
						label="组件宽度"
					>
						<el-input
							v-model="activeDataTemp.style.width"
							placeholder="请输入组件宽度"
							clearable
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__vModel__ !== undefined"
						label="默认值"
					>
						<el-input
							:value="
								setDefaultValue(
									activeDataTemp.__config__.defaultValue
								)
							"
							placeholder="请输入默认值"
							@input="onDefaultValueInput"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.tag ===
							'el-checkbox-group'
						"
						label="至少应选"
					>
						<el-input-number
							:value="activeDataTemp.min"
							:min="0"
							placeholder="至少应选"
							@input="
								$set(
									activeDataTemp,
									'min',
									$event ? $event : undefined
								)
							"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.tag ===
							'el-checkbox-group'
						"
						label="最多可选"
					>
						<el-input-number
							:value="activeDataTemp.max"
							:min="0"
							placeholder="最多可选"
							@input="
								$set(
									activeDataTemp,
									'max',
									$event ? $event : undefined
								)
							"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__slot__ &&
							activeDataTemp.__slot__.prepend !== undefined
						"
						label="前缀"
					>
						<el-input
							v-model="activeDataTemp.__slot__.prepend"
							placeholder="请输入前缀"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__slot__ &&
							activeDataTemp.__slot__.append !== undefined
						"
						label="后缀"
					>
						<el-input
							v-model="activeDataTemp.__slot__.append"
							placeholder="请输入后缀"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['prefix-icon'] !== undefined"
						label="前图标"
					>
						<el-input
							v-model="activeDataTemp['prefix-icon']"
							placeholder="请输入前图标名称"
						>
							<template v-slot:append>
								<el-button
									icon="el-icon-thumb"
									@click="openIconsDialog('prefix-icon')"
								>
									选择
								</el-button>
							</template>
						</el-input>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['suffix-icon'] !== undefined"
						label="后图标"
					>
						<el-input
							v-model="activeDataTemp['suffix-icon']"
							placeholder="请输入后图标名称"
						>
							<template v-slot:append>
								<el-button
									icon="el-icon-thumb"
									@click="openIconsDialog('suffix-icon')"
								>
									选择
								</el-button>
							</template>
						</el-input>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp['icon'] !== undefined &&
							activeDataTemp.__config__.tag === 'el-button'
						"
						label="按钮图标"
					>
						<el-input
							v-model="activeDataTemp['icon']"
							placeholder="请输入按钮图标名称"
						>
							<template v-slot:append>
								<el-button
									icon="el-icon-thumb"
									@click="openIconsDialog('icon')"
								>
									选择
								</el-button>
							</template>
						</el-input>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-cascader'"
						label="选项分隔符"
					>
						<el-input
							v-model="activeDataTemp.separator"
							placeholder="请输入选项分隔符"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.autosize !== undefined"
						label="最小行数"
					>
						<el-input-number
							v-model="activeDataTemp.autosize.minRows"
							:min="1"
							placeholder="最小行数"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.autosize !== undefined"
						label="最大行数"
					>
						<el-input-number
							v-model="activeDataTemp.autosize.maxRows"
							:min="1"
							placeholder="最大行数"
						/>
					</el-form-item>
					<el-form-item v-if="isShowMin" label="最小值">
						<el-input-number
							v-model="activeDataTemp.min"
							placeholder="最小值"
						/>
					</el-form-item>
					<el-form-item v-if="isShowMax" label="最大值">
						<el-input-number
							v-model="activeDataTemp.max"
							placeholder="最大值"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.height !== undefined"
						label="组件高度"
					>
						<el-input-number
							v-model="activeDataTemp.height"
							placeholder="高度"
							@input="changeRenderKey"
						/>
					</el-form-item>
					<el-form-item v-if="isShowStep" label="步长">
						<el-input-number
							v-model="activeDataTemp.step"
							placeholder="步数"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.tag === 'el-input-number'
						"
						label="精度"
					>
						<el-input-number
							v-model="activeDataTemp.precision"
							:min="0"
							placeholder="精度"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.tag === 'el-input-number'
						"
						label="按钮位置"
					>
						<el-radio-group
							v-model="activeDataTemp['controls-position']"
						>
							<el-radio-button label=""> 默认 </el-radio-button>
							<el-radio-button label="right">
								右侧
							</el-radio-button>
						</el-radio-group>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.maxlength !== undefined"
						label="最多输入"
					>
						<el-input
							v-model="activeDataTemp.maxlength"
							placeholder="请输入字符长度"
						>
							<template v-slot:append> 个字符 </template>
						</el-input>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['active-text'] !== undefined"
						label="开启提示"
					>
						<el-input
							v-model="activeDataTemp['active-text']"
							placeholder="请输入开启提示"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['inactive-text'] !== undefined"
						label="关闭提示"
					>
						<el-input
							v-model="activeDataTemp['inactive-text']"
							placeholder="请输入关闭提示"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['active-value'] !== undefined"
						label="开启值"
					>
						<el-input
							:value="
								setDefaultValue(activeDataTemp['active-value'])
							"
							placeholder="请输入开启值"
							@input="onSwitchValueInput($event, 'active-value')"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['inactive-value'] !== undefined"
						label="关闭值"
					>
						<el-input
							:value="
								setDefaultValue(
									activeDataTemp['inactive-value']
								)
							"
							placeholder="请输入关闭值"
							@input="
								onSwitchValueInput($event, 'inactive-value')
							"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.type !== undefined &&
							'el-date-picker' === activeDataTemp.__config__.tag
						"
						label="时间类型"
					>
						<el-select
							v-model="activeDataTemp.type"
							placeholder="请选择时间类型"
							:style="{ width: '100%' }"
							@change="dateTypeChange"
						>
							<el-option
								v-for="(item, index) in dateOptions"
								:key="index"
								:label="item.label"
								:value="item.value"
							/>
						</el-select>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.name !== undefined"
						label="文件字段名"
					>
						<el-input
							v-model="activeDataTemp.name"
							placeholder="请输入上传文件字段名"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.accept !== undefined"
						label="文件类型"
					>
						<el-select
							v-model="activeDataTemp.accept"
							placeholder="请选择文件类型"
							:style="{ width: '100%' }"
							clearable
						>
							<el-option label="图片" value="image/*" />
							<el-option label="视频" value="video/*" />
							<el-option label="音频" value="audio/*" />
							<el-option label="excel" value=".xls,.xlsx" />
							<el-option label="word" value=".doc,.docx" />
							<el-option label="pdf" value=".pdf" />
							<el-option label="txt" value=".txt" />
						</el-select>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.fileSize !== undefined"
						label="文件大小"
					>
						<el-input
							v-model.number="activeDataTemp.__config__.fileSize"
							placeholder="请输入文件大小"
						>
							<template v-slot:append>
								<el-select
									v-model="activeDataTemp.__config__.sizeUnit"
									:style="{ width: '66px' }"
								>
									<el-option label="KB" value="KB" />
									<el-option label="MB" value="MB" />
									<el-option label="GB" value="GB" />
								</el-select>
							</template>
						</el-input>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.action !== undefined"
						label="上传地址"
					>
						<el-input
							v-model="activeDataTemp.action"
							placeholder="请输入上传地址"
							clearable
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['list-type'] !== undefined"
						label="列表类型"
					>
						<el-radio-group
							v-model="activeDataTemp['list-type']"
							size="small"
						>
							<el-radio-button label="text">
								text
							</el-radio-button>
							<el-radio-button label="picture">
								picture
							</el-radio-button>
							<el-radio-button label="picture-card">
								picture-card
							</el-radio-button>
						</el-radio-group>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.type !== undefined &&
							activeDataTemp.__config__.tag === 'el-button'
						"
						label="按钮类型"
					>
						<el-select
							v-model="activeDataTemp.type"
							:style="{ width: '100%' }"
						>
							<el-option label="primary" value="primary" />
							<el-option label="success" value="success" />
							<el-option label="warning" value="warning" />
							<el-option label="danger" value="danger" />
							<el-option label="info" value="info" />
							<el-option label="text" value="text" />
						</el-select>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.buttonText !== undefined
						"
						v-show="'picture-card' !== activeDataTemp['list-type']"
						label="按钮文字"
					>
						<el-input
							v-model="activeDataTemp.__config__.buttonText"
							placeholder="请输入按钮文字"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-button'"
						label="按钮文字"
					>
						<el-input
							v-model="activeDataTemp.__slot__.default"
							placeholder="请输入按钮文字"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['range-separator'] !== undefined"
						label="分隔符"
					>
						<el-input
							v-model="activeDataTemp['range-separator']"
							placeholder="请输入分隔符"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['picker-options'] !== undefined"
						label="时间段"
					>
						<el-input
							v-model="
								activeDataTemp['picker-options'].selectableRange
							"
							placeholder="请输入时间段"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.format !== undefined"
						label="时间格式"
					>
						<el-input
							:value="activeDataTemp.format"
							placeholder="请输入时间格式"
							@input="setTimeValue($event)"
						/>
					</el-form-item>
					<template
						v-if="
							[
								'el-checkbox-group',
								'el-radio-group',
								'el-select',
							].indexOf(activeDataTemp.__config__.tag) > -1
						"
					>
						<el-divider>选项</el-divider>
						<draggable
							:list="activeDataTemp.__slot__.options"
							:animation="340"
							group="selectItem"
							handle=".option-drag"
						>
							<template #default>
								<div
									v-for="(item, index) in activeDataTemp
										.__slot__.options"
									:key="index"
									class="select-item"
								>
									<div class="select-line-icon option-drag">
										<i class="el-icon-s-operation" />
									</div>
									<el-input
										v-model="item.label"
										placeholder="选项名"
										size="small"
									/>
									<el-input
										placeholder="选项值"
										size="small"
										:value="item.value"
										@input="setOptionValue(item, $event)"
									/>
									<div
										class="close-btn select-line-icon"
										@click="
											activeDataTemp.__slot__.options.splice(
												index,
												1
											)
										"
									>
										<i class="el-icon-remove-outline" />
									</div>
								</div>
							</template>
						</draggable>
						<div style="margin-left: 20px">
							<el-button
								style="padding-bottom: 0"
								icon="el-icon-circle-plus-outline"
								type="text"
								@click="addSelectItem"
							>
								添加选项
							</el-button>
						</div>
						<el-divider />
					</template>

					<template
						v-if="
							['el-cascader', 'el-table'].includes(
								activeDataTemp.__config__.tag
							)
						"
					>
						<el-divider>选项</el-divider>
						<el-form-item
							v-if="activeDataTemp.__config__.dataType"
							label="数据类型"
						>
							<el-radio-group
								v-model="activeDataTemp.__config__.dataType"
								size="small"
							>
								<el-radio-button label="dynamic">
									动态数据
								</el-radio-button>
								<el-radio-button label="static">
									静态数据
								</el-radio-button>
							</el-radio-group>
						</el-form-item>

						<template
							v-if="
								activeDataTemp.__config__.dataType === 'dynamic'
							"
						>
							<el-form-item label="接口地址">
								<el-input
									v-model="activeDataTemp.__config__.url"
									:title="activeDataTemp.__config__.url"
									placeholder="请输入接口地址"
									clearable
									@blur="$emit('fetch-data', activeDataTemp)"
								>
									<template v-slot:prepend>
										<el-select
											v-model="
												activeDataTemp.__config__.method
											"
											:style="{ width: '85px' }"
											@change="
												$emit(
													'fetch-data',
													activeDataTemp
												)
											"
										>
											<el-option
												label="get"
												value="get"
											/>
											<el-option
												label="post"
												value="post"
											/>
											<el-option
												label="put"
												value="put"
											/>
											<el-option
												label="delete"
												value="delete"
											/>
										</el-select>
									</template>
								</el-input>
							</el-form-item>
							<el-form-item label="数据位置">
								<el-input
									v-model="activeDataTemp.__config__.dataPath"
									placeholder="请输入数据位置"
									@blur="$emit('fetch-data', activeDataTemp)"
								/>
							</el-form-item>

							<template
								v-if="
									activeDataTemp.props &&
									activeDataTemp.props.props
								"
							>
								<el-form-item label="标签键名">
									<el-input
										v-model="
											activeDataTemp.props.props.label
										"
										placeholder="请输入标签键名"
									/>
								</el-form-item>
								<el-form-item label="值键名">
									<el-input
										v-model="
											activeDataTemp.props.props.value
										"
										placeholder="请输入值键名"
									/>
								</el-form-item>
								<el-form-item label="子级键名">
									<el-input
										v-model="
											activeDataTemp.props.props.children
										"
										placeholder="请输入子级键名"
									/>
								</el-form-item>
							</template>
						</template>

						<!-- 级联选择静态树 -->
						<el-tree
							v-if="
								activeDataTemp.__config__.dataType === 'static'
							"
							draggable
							:data="activeDataTemp.options"
							node-key="id"
							:expand-on-click-node="false"
							:render-content="renderContent"
						/>
						<div
							v-if="
								activeDataTemp.__config__.dataType === 'static'
							"
							style="margin-left: 20px"
						>
							<el-button
								style="padding-bottom: 0"
								icon="el-icon-circle-plus-outline"
								type="text"
								@click="addTreeItem"
							>
								添加父级
							</el-button>
						</div>
						<el-divider />
					</template>

					<el-form-item
						v-if="
							activeDataTemp.__config__.optionType !== undefined
						"
						label="选项样式"
					>
						<el-radio-group
							v-model="activeDataTemp.__config__.optionType"
						>
							<el-radio-button label="default">
								默认
							</el-radio-button>
							<el-radio-button label="button">
								按钮
							</el-radio-button>
						</el-radio-group>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['active-color'] !== undefined"
						label="开启颜色"
					>
						<el-color-picker
							v-model="activeDataTemp['active-color']"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['inactive-color'] !== undefined"
						label="关闭颜色"
					>
						<el-color-picker
							v-model="activeDataTemp['inactive-color']"
						/>
					</el-form-item>

					<el-form-item
						v-if="
							activeDataTemp.__config__.showLabel !== undefined &&
							activeDataTemp.__config__.labelWidth !== undefined
						"
						label="显示标签"
					>
						<el-switch
							v-model="activeDataTemp.__config__.showLabel"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.branding !== undefined"
						label="品牌烙印"
					>
						<el-switch
							v-model="activeDataTemp.branding"
							@input="changeRenderKey"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['allow-half'] !== undefined"
						label="允许半选"
					>
						<el-switch v-model="activeDataTemp['allow-half']" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['show-text'] !== undefined"
						label="辅助文字"
					>
						<el-switch
							v-model="activeDataTemp['show-text']"
							@change="rateTextChange"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['show-score'] !== undefined"
						label="显示分数"
					>
						<el-switch
							v-model="activeDataTemp['show-score']"
							@change="rateScoreChange"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['show-stops'] !== undefined"
						label="显示间断点"
					>
						<el-switch v-model="activeDataTemp['show-stops']" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.range !== undefined"
						label="范围选择"
					>
						<el-switch
							v-model="activeDataTemp.range"
							@change="rangeChange"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.border !== undefined &&
							activeDataTemp.__config__.optionType === 'default'
						"
						label="是否带边框"
					>
						<el-switch v-model="activeDataTemp.__config__.border" />
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.tag === 'el-color-picker'
						"
						label="颜色格式"
					>
						<el-select
							v-model="activeDataTemp['color-format']"
							placeholder="请选择颜色格式"
							:style="{ width: '100%' }"
							clearable
							@change="colorFormatChange"
						>
							<el-option
								v-for="(item, index) in colorFormatOptions"
								:key="index"
								:label="item.label"
								:value="item.value"
							/>
						</el-select>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.size !== undefined &&
							(activeDataTemp.__config__.optionType ===
								'button' ||
								activeDataTemp.__config__.border ||
								activeDataTemp.__config__.tag ===
									'el-color-picker' ||
								activeDataTemp.__config__.tag === 'el-button')
						"
						label="组件尺寸"
					>
						<el-radio-group v-model="activeDataTemp.size">
							<el-radio-button label="medium">
								中等
							</el-radio-button>
							<el-radio-button label="small">
								较小
							</el-radio-button>
							<el-radio-button label="mini">
								迷你
							</el-radio-button>
						</el-radio-group>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['show-word-limit'] !== undefined"
						label="输入统计"
					>
						<el-switch
							v-model="activeDataTemp['show-word-limit']"
						/>
					</el-form-item>
					<el-form-item
						v-if="
							activeDataTemp.__config__.tag === 'el-input-number'
						"
						label="严格步数"
					>
						<el-switch v-model="activeDataTemp['step-strictly']" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-cascader'"
						label="任选层级"
					>
						<el-switch
							v-model="activeDataTemp.props.props.checkStrictly"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-cascader'"
						label="是否多选"
					>
						<el-switch
							v-model="activeDataTemp.props.props.multiple"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-cascader'"
						label="展示全路径"
					>
						<el-switch
							v-model="activeDataTemp['show-all-levels']"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-cascader'"
						label="可否筛选"
					>
						<el-switch v-model="activeDataTemp.filterable" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.clearable !== undefined"
						label="能否清空"
					>
						<el-switch v-model="activeDataTemp.clearable" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.showTip !== undefined"
						label="显示提示"
					>
						<el-switch
							v-model="activeDataTemp.__config__.showTip"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-upload'"
						label="多选文件"
					>
						<el-switch v-model="activeDataTemp.multiple" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp['auto-upload'] !== undefined"
						label="自动上传"
					>
						<el-switch v-model="activeDataTemp['auto-upload']" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.readonly !== undefined"
						label="是否只读"
					>
						<el-switch v-model="activeDataTemp.readonly" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.disabled !== undefined"
						label="是否禁用"
					>
						<el-switch v-model="activeDataTemp.disabled" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-select'"
						label="能否搜索"
					>
						<el-switch v-model="activeDataTemp.filterable" />
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.tag === 'el-select'"
						label="是否多选"
					>
						<el-switch
							v-model="activeDataTemp.multiple"
							@change="multipleChange"
						/>
					</el-form-item>
					<el-form-item
						v-if="activeDataTemp.__config__.required !== undefined"
						label="是否必填"
					>
						<el-switch
							v-model="activeDataTemp.__config__.required"
						/>
					</el-form-item>

					<template v-if="activeDataTemp.__config__.layoutTree">
						<el-divider>布局结构树</el-divider>
						<el-tree
							:data="[activeDataTemp.__config__]"
							:props="layoutTreeProps"
							node-key="renderKey"
							default-expand-all
							draggable
						>
							<template #default="{ node, data }">
								<span class="node-label">
									<svg-icon
										class="node-icon"
										:icon-class="
											data.__config__
												? data.__config__.tagIcon
												: data.tagIcon
										"
									/>
									{{ node.label }}
								</span>
							</template>
						</el-tree>
					</template>

					<template
						v-if="Array.isArray(activeDataTemp.__config__.regList)"
					>
						<el-divider>正则校验</el-divider>
						<div
							v-for="(item, index) in activeDataTemp.__config__
								.regList"
							:key="index"
							class="reg-item"
						>
							<span
								class="close-btn"
								@click="
									activeDataTemp.__config__.regList.splice(
										index,
										1
									)
								"
							>
								<i class="el-icon-close" />
							</span>
							<el-form-item label="表达式">
								<el-input
									v-model="item.pattern"
									placeholder="请输入正则"
								/>
							</el-form-item>
							<el-form-item
								label="错误提示"
								style="margin-bottom: 0"
							>
								<el-input
									v-model="item.message"
									placeholder="请输入错误提示"
								/>
							</el-form-item>
						</div>
						<div style="margin-left: 20px">
							<el-button
								icon="el-icon-circle-plus-outline"
								type="text"
								@click="addReg"
							>
								添加规则
							</el-button>
						</div>
					</template>
				</el-form>
				<!-- 表单属性 -->
				<el-form
					v-show="currentTab === 'form'"
					size="small"
					label-width="90px"
				>
					<el-form-item label="表单名">
						<el-input
							v-model="formConfTemp.formRef"
							placeholder="请输入表单名（ref）"
						/>
					</el-form-item>
					<el-form-item label="表单模型">
						<el-input
							v-model="formConfTemp.formModel"
							placeholder="请输入数据模型"
						/>
					</el-form-item>
					<el-form-item label="校验模型">
						<el-input
							v-model="formConfTemp.formRules"
							placeholder="请输入校验模型"
						/>
					</el-form-item>
					<el-form-item label="表单尺寸">
						<el-radio-group v-model="formConfTemp.size">
							<el-radio-button label="medium">
								中等
							</el-radio-button>
							<el-radio-button label="small">
								较小
							</el-radio-button>
							<el-radio-button label="mini">
								迷你
							</el-radio-button>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="标签对齐">
						<el-radio-group v-model="formConfTemp.labelPosition">
							<el-radio-button label="left">
								左对齐
							</el-radio-button>
							<el-radio-button label="right">
								右对齐
							</el-radio-button>
							<el-radio-button label="top">
								顶部对齐
							</el-radio-button>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="标签宽度">
						<el-input
							v-model.number="formConfTemp.labelWidth"
							type="number"
							placeholder="请输入标签宽度"
						/>
					</el-form-item>
					<el-form-item label="栅格间隔">
						<el-input-number
							v-model="formConfTemp.gutter"
							:min="0"
							placeholder="栅格间隔"
						/>
					</el-form-item>
					<el-form-item label="禁用表单">
						<el-switch v-model="formConfTemp.disabled" />
					</el-form-item>
					<el-form-item label="表单按钮">
						<el-switch v-model="formConfTemp.formBtns" />
					</el-form-item>
					<el-form-item label="显示未选中组件边框">
						<el-switch
							v-model="formConfTemp.unFocusedComponentBorder"
						/>
					</el-form-item>
				</el-form>
			</el-scrollbar>
		</div>

		<treeNode-dialog
			v-model:visible="dialogVisible"
			title="添加选项"
			@commit="addNode"
		/>
		<icons-dialog
			v-model:visible="iconsVisible"
			:current="activeDataTemp[currentIconModel]"
			@select="setIcon"
		/>
	</div>
</template>

<script>
import { isArray } from "util";
import TreeNodeDialog from "@/views/index/TreeNodeDialog";
import { isNumberStr } from "@/utils/index";
import IconsDialog from "./IconsDialog";
import {
	inputComponents,
	selectComponents,
	layoutComponents,
} from "@/components/generator/config";
import { saveFormConf } from "@/utils/dbForm";

const dateTimeFormat = {
	date: "yyyy-MM-dd",
	week: "yyyy 第 WW 周",
	month: "yyyy-MM",
	year: "yyyy",
	datetime: "yyyy-MM-dd HH:mm:ss",
	daterange: "yyyy-MM-dd",
	monthrange: "yyyy-MM",
	datetimerange: "yyyy-MM-dd HH:mm:ss",
};

// 使changeRenderKey在目标组件改变时可用
const needRerenderList = ["tinymce"];

export default {
	components: {
		TreeNodeDialog,
		IconsDialog,
	},
	props: {
		// "showField", "activeDataTemp", "formConfTemp"
		showField: {
			type: Boolean,
			required: true,
		},
		activeData: {
			type: Object,
			required: true,
		},
		formConf: {
			type: Object,
			required: true,
		},
	},
	data() {
		return {
			currentTab: "field",
			currentNode: null,
			dialogVisible: false,
			iconsVisible: false,
			currentIconModel: null,
			dateTypeOptions: [
				{
					label: "日(date)",
					value: "date",
				},
				{
					label: "周(week)",
					value: "week",
				},
				{
					label: "月(month)",
					value: "month",
				},
				{
					label: "年(year)",
					value: "year",
				},
				{
					label: "日期时间(datetime)",
					value: "datetime",
				},
			],
			dateRangeTypeOptions: [
				{
					label: "日期范围(daterange)",
					value: "daterange",
				},
				{
					label: "月范围(monthrange)",
					value: "monthrange",
				},
				{
					label: "日期时间范围(datetimerange)",
					value: "datetimerange",
				},
			],
			colorFormatOptions: [
				{
					label: "hex",
					value: "hex",
				},
				{
					label: "rgb",
					value: "rgb",
				},
				{
					label: "rgba",
					value: "rgba",
				},
				{
					label: "hsv",
					value: "hsv",
				},
				{
					label: "hsl",
					value: "hsl",
				},
			],
			justifyOptions: [
				{
					label: "start",
					value: "start",
				},
				{
					label: "end",
					value: "end",
				},
				{
					label: "center",
					value: "center",
				},
				{
					label: "space-around",
					value: "space-around",
				},
				{
					label: "space-between",
					value: "space-between",
				},
			],
			layoutTreeProps: {
				label(data, node) {
					const config = data.__config__;
					return (
						data.componentName ||
						`${config.label}: ${data.__vModel__}`
					);
				},
			},
			activeDataTemp: {},
			formConfTemp: {},
		};
	},
	computed: {
		documentLink() {
			return (
				this.activeDataTemp.__config__.document ||
				"https://element.eleme.cn/#/zh-CN/component/installation"
			);
		},
		dateOptions() {
			if (
				this.activeDataTemp.type !== undefined &&
				this.activeDataTemp.__config__.tag === "el-date-picker"
			) {
				if (this.activeDataTemp["start-placeholder"] === undefined) {
					return this.dateTypeOptions;
				}
				return this.dateRangeTypeOptions;
			}
			return [];
		},
		tagList() {
			return [
				{
					label: "输入型组件",
					options: inputComponents,
				},
				{
					label: "选择型组件",
					options: selectComponents,
				},
			];
		},
		activeTag() {
			return this.activeDataTemp.__config__.tag;
		},
		isShowMin() {
			return (
				["el-input-number", "el-slider"].indexOf(this.activeTag) > -1
			);
		},
		isShowMax() {
			return (
				["el-input-number", "el-slider", "el-rate"].indexOf(
					this.activeTag
				) > -1
			);
		},
		isShowStep() {
			return (
				["el-input-number", "el-slider"].indexOf(this.activeTag) > -1
			);
		},
	},
	watch: {
		formConfTemp: {
			handler(val) {
				saveFormConf(val);
			},
			deep: true,
		},
		// 父类会修改当前选择组件值，得监听随时改变
		activeData: {
			handler(val) {
				this.activeDataTemp = val;
			},
			deep: true,
		},
		formConf: {
			handler(val) {
				this.formConfTemp = val;
			},
			deep: true,
		},
	},
	methods: {
		addReg() {
			this.activeDataTemp.__config__.regList.push({
				pattern: "",
				message: "",
			});
		},
		addSelectItem() {
			this.activeDataTemp.__slot__.options.push({
				label: "",
				value: "",
			});
		},
		addTreeItem() {
			++this.idGlobal;
			this.dialogVisible = true;
			this.currentNode = this.activeDataTemp.options;
		},
		renderContent(h, { node, data, store }) {
			return (
				<div class="custom-tree-node">
					<span>{node.label}</span>
					<span class="node-operation">
						<i
							on-click={() => this.append(data)}
							class="el-icon-plus"
							title="添加"
						></i>
						<i
							on-click={() => this.remove(node, data)}
							class="el-icon-delete"
							title="删除"
						></i>
					</span>
				</div>
			);
		},
		append(data) {
			if (!data.children) {
				this.$set(data, "children", []);
			}
			this.dialogVisible = true;
			this.currentNode = data.children;
		},
		remove(node, data) {
			this.activeDataTemp.__config__.defaultValue = []; // 避免删除时报错
			const { parent } = node;
			const children = parent.data.children || parent.data;
			const index = children.findIndex((d) => d.id === data.id);
			children.splice(index, 1);
		},
		addNode(data) {
			this.currentNode.push(data);
		},
		setOptionValue(item, val) {
			item.value = isNumberStr(val) ? +val : val;
		},
		setDefaultValue(val) {
			if (Array.isArray(val)) {
				return val.join(",");
			}
			// if (['string', 'number'].indexOf(typeof val) > -1) {
			//   return val
			// }
			if (typeof val === "boolean") {
				return `${val}`;
			}
			return val;
		},
		onDefaultValueInput(str) {
			if (isArray(this.activeDataTemp.__config__.defaultValue)) {
				// 数组
				this.$set(
					this.activeDataTemp.__config__,
					"defaultValue",
					str.split(",").map((val) => (isNumberStr(val) ? +val : val))
				);
			} else if (["true", "false"].indexOf(str) > -1) {
				// 布尔
				this.$set(
					this.activeDataTemp.__config__,
					"defaultValue",
					JSON.parse(str)
				);
			} else {
				// 字符串和数字
				this.$set(
					this.activeDataTemp.__config__,
					"defaultValue",
					isNumberStr(str) ? +str : str
				);
			}
		},
		onSwitchValueInput(val, name) {
			if (["true", "false"].indexOf(val) > -1) {
				this.$set(this.activeDataTemp, name, JSON.parse(val));
			} else {
				this.$set(
					this.activeDataTemp,
					name,
					isNumberStr(val) ? +val : val
				);
			}
		},
		setTimeValue(val, type) {
			const valueFormat = type === "week" ? dateTimeFormat.date : val;
			this.$set(this.activeDataTemp.__config__, "defaultValue", null);
			this.$set(this.activeDataTemp, "value-format", valueFormat);
			this.$set(this.activeDataTemp, "format", val);
		},
		spanChange(val) {
			this.formConfTemp.span = val;
		},
		multipleChange(val) {
			this.$set(
				this.activeDataTemp.__config__,
				"defaultValue",
				val ? [] : ""
			);
		},
		dateTypeChange(val) {
			this.setTimeValue(dateTimeFormat[val], val);
		},
		rangeChange(val) {
			this.$set(
				this.activeDataTemp.__config__,
				"defaultValue",
				val
					? [this.activeDataTemp.min, this.activeDataTemp.max]
					: this.activeDataTemp.min
			);
		},
		rateTextChange(val) {
			if (val) this.activeDataTemp["show-score"] = false;
		},
		rateScoreChange(val) {
			if (val) this.activeDataTemp["show-text"] = false;
		},
		colorFormatChange(val) {
			this.activeDataTemp.__config__.defaultValue = null;
			this.activeDataTemp["show-alpha"] = val.indexOf("a") > -1;
			this.activeDataTemp.__config__.renderKey = +new Date(); // 更新renderKey,重新渲染该组件
		},
		openIconsDialog(model) {
			this.iconsVisible = true;
			this.currentIconModel = model;
		},
		setIcon(val) {
			this.activeDataTemp[this.currentIconModel] = val;
		},
		tagChange(tagIcon) {
			let target = inputComponents.find(
				(item) => item.__config__.tagIcon === tagIcon
			);
			if (!target)
				target = selectComponents.find(
					(item) => item.__config__.tagIcon === tagIcon
				);
			this.$emit("tag-change", target);
		},
		changeRenderKey() {
			if (needRerenderList.includes(this.activeDataTemp.__config__.tag)) {
				this.activeDataTemp.__config__.renderKey = +new Date();
			}
		},
	},
	created() {
		this.activeDataTemp = this.activeData;

		this.formConfTemp = this.formConf;
	},
};
</script>

<style lang="scss" scoped>
.right-board {
	width: 350px;
	position: absolute;
	right: 0;
	top: 0;
	padding-top: 3px;
	.field-box {
		position: relative;
		height: calc(100vh - 42px);
		box-sizing: border-box;
		overflow: hidden;
	}
	.el-scrollbar {
		height: 100%;
	}
}
.select-item {
	display: flex;
	border: 1px dashed #fff;
	box-sizing: border-box;
	& .close-btn {
		cursor: pointer;
		color: #f56c6c;
	}
	& .el-input + .el-input {
		margin-left: 4px;
	}
}
.select-item + .select-item {
	margin-top: 4px;
}
.select-item.sortable-chosen {
	border: 1px dashed #409eff;
}
.select-line-icon {
	line-height: 32px;
	font-size: 22px;
	padding: 0 4px;
	color: #777;
}
.option-drag {
	cursor: move;
}
.time-range {
	.el-date-editor {
		width: 227px;
	}
	::v-deep .el-icon-time {
		display: none;
	}
}
.document-link {
	position: absolute;
	display: block;
	width: 26px;
	height: 26px;
	top: 0;
	left: 0;
	cursor: pointer;
	background: #409eff;
	z-index: 1;
	border-radius: 0 0 6px 0;
	text-align: center;
	line-height: 26px;
	color: #fff;
	font-size: 18px;
}
.node-label {
	font-size: 14px;
}
.node-icon {
	color: #bebfc3;
}
</style>
