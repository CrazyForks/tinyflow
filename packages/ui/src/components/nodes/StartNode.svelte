<script lang="ts">
    import NodeWrapper from '../core/NodeWrapper.svelte';
    import { Heading } from '../base';
    import { Button } from '../base/index.js';
    import { type NodeProps } from '@xyflow/svelte';
    import DefinedParameterList from '../core/DefinedParameterList.svelte';
    import { getCurrentNodeId } from '#components/utils/NodeUtils';
    import { useAddParameter } from '../utils/useAddParameter.svelte';

    const { data, ...rest }: {
        data: NodeProps['data'],
        [key: string]: any
    } = $props();

    const currentNodeId = getCurrentNodeId();
    const { addParameter } = useAddParameter();

</script>


<NodeWrapper {...rest} {data} allowExecute={false} showTargetHandle={false} allowSettingOfCondition={false}>
    {#snippet icon()}
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
            <path
                d="M12 22C6.47715 22 2 17.5228 2 12C2 6.47715 6.47715 2 12 2C17.5228 2 22 6.47715 22 12C22 17.5228 17.5228 22 12 22ZM12 20C16.4183 20 20 16.4183 20 12C20 7.58172 16.4183 4 12 4C7.58172 4 4 7.58172 4 12C4 16.4183 7.58172 20 12 20ZM12 15C10.3431 15 9 13.6569 9 12C9 10.3431 10.3431 9 12 9C13.6569 9 15 10.3431 15 12C15 13.6569 13.6569 15 12 15Z"></path>
        </svg>
    {/snippet}
    <div class="heading">
        <Heading level={3}>输入参数</Heading>
        <Button class="input-btn-more" style="margin-left: auto" onclick={()=>{
            addParameter(currentNodeId, "parameters", {refType: "input"});
        }}>
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
                <path d="M11 11V5H13V11H19V13H13V19H11V13H5V11H11Z"></path>
            </svg>
        </Button>
    </div>
    <DefinedParameterList />
</NodeWrapper>

<style lang="less">

  .heading {
    display: flex;
    margin-bottom: 10px;
  }

  :global(.input-btn-more) {
    border: 1px solid transparent;
    padding: 3px;

    &:hover {
      background: #eee;
      border: 1px solid transparent;
    }
  }
</style>


