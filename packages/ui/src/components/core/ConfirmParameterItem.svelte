<script lang="ts">
    import { Input, MenuButton, Textarea } from '../base';
    import { Button, FloatingTrigger, Select } from '../base/index.js';
    import { getCurrentNodeId } from '#components/utils/NodeUtils';
    import { useNodesData, useSvelteFlow } from '@xyflow/svelte';
    import { useRefOptions } from '../utils/useRefOptions.svelte';
    import type { ConfirmParameter, Parameter } from '#types';


    const { parameter, index, dataKeyName, useChildrenOnly }: {
        parameter: ConfirmParameter,
        index: number,
        dataKeyName: string,
        useChildrenOnly?: boolean,
    } = $props();


    let currentNodeId = getCurrentNodeId();
    let node = useNodesData(currentNodeId);
    let param = $derived.by(() => {
        return {
            ...parameter,
            ...(node?.current?.data?.[dataKeyName] as Array<Parameter>)[index]
        };
    });

    const { updateNodeData } = useSvelteFlow();


    const updateParam = (key: string, value: any) => {
        updateNodeData(currentNodeId, (node) => {
            let parameters = node.data?.[dataKeyName] as Array<Parameter>;
            parameters[index] = {
                ...parameters[index],
                [key]: value
            };
            return {
                [dataKeyName]: parameters
            };
        });
    };


    const updateParamByEvent = (name: string, event: Event) => {
        const newValue = (event.target as any).value;
        updateParam(name, newValue);
    };

    const updateRef = (item: any) => {
        const newValue = item.value;
        updateParam('ref', newValue);
    };

    const updateSelectionMode = (item: any) => {
        const newValue = item.value;
        updateParam('selectionMode', newValue);
    };


    const updateSelectionDataType = (item: any) => {
        const newValue = item.value;
        updateParam('selectionDataType', newValue);
    };

    const updateRequired = (item: any) => {
        const newValue = item.target.checked;
        updateParam('required', newValue);
    };


    let triggerObject: any;
    const handleDelete = () => {
        updateNodeData(currentNodeId, (node) => {
            let parameters = node.data?.[dataKeyName] as Array<Parameter>;
            parameters.splice(index, 1);
            return {
                [dataKeyName]: [...parameters]
            };
        });
        triggerObject?.hide();
    };
    let selectItems = useRefOptions(useChildrenOnly);

    const selectionDataTypes = [
        { label: '文字', value: 'text' },
        { label: '图片', value: 'image' },
        { label: '视频', value: 'video' },
        { label: '音频', value: 'audio' },
        { label: '文件', value: 'file' },
        { label: '其他', value: 'other' }
    ];

    const selectionModes = [
        { label: '单选', value: 'single' },
        { label: '多选', value: 'multiple' }
    ];


</script>


<div class="input-item">
    <Input style="width: 100%;" value={param.name} placeholder="请输入参数名称"
           disabled={param.nameDisabled === true}
           oninput={(event)=>updateParamByEvent('name', event)} />
</div>
<div class="input-item">
    {#if param.refType === 'fixed'}
        <Input value={param.value} placeholder="请输入参数值" oninput={(event)=>updateParamByEvent('value', event)} />
    {:else if (param.refType !== 'input')}
        <Select items={selectItems.current} style="width: 100%" defaultValue={["ref"]} value={[param.ref]}
                expandAll
                onSelect={updateRef} />
    {/if}
</div>
<div class="input-item">
    <FloatingTrigger placement="bottom" bind:this={triggerObject}>
        <MenuButton />
        {#snippet floating()}
            <div class="input-more-setting">
                <div class="input-more-item">
                    数据类型：
                    <Select items={selectionDataTypes} style="width: 100%" defaultValue={["text"]}
                            value={param.selectionDataType ? [param.selectionDataType] : []}
                            onSelect={updateSelectionDataType}
                    />
                </div>
                <div class="input-more-item">
                    确认方式：
                    <Select items={selectionModes} style="width: 100%" defaultValue={["single"]}
                            value={param.selectionMode ? [param.selectionMode] : []}
                            onSelect={updateSelectionMode}
                    />
                </div>

                <div class="input-more-item">
                    数据标题：
                    <Textarea rows={1} style="width: 100%;" onchange={(event)=>{
                        updateParamByEvent('formLabel', event)
                    }} value={param.formLabel} />
                </div>

                <div class="input-more-item">
                    数据描述：
                    <Textarea rows={2} style="width: 100%;" onchange={(event)=>{
                        updateParamByEvent('formDescription', event)
                    }} value={param.formDescription} />
                </div>

                <label class="input-item-inline">
                    <span>是否必填：</span>
                    <input type="checkbox" checked={false} onchange={updateRequired} />
                </label>


                <div class="input-more-item">
                    <Button onclick={handleDelete}>删除</Button>
                </div>
            </div>
        {/snippet}
    </FloatingTrigger>
</div>


<style lang="less">

  .input-item {
    display: flex;
    align-items: center;
  }

  .input-item-inline {
    display: flex;
    align-items: center;
    font-size: 12px;
    color: #666;
  }

  .input-more-setting {
    display: flex;
    flex-direction: column;
    gap: 10px;
    padding: 10px;
    background: #fff;
    border: 1px solid #ddd;
    border-radius: 5px;
    width: 200px;
    box-shadow: 0 0 10px 2px rgba(0, 0, 0, 0.1);

    .input-more-item {
      display: flex;
      flex-direction: column;
      gap: 3px;
      font-size: 12px;
      color: #666;
    }
  }

</style>


