Example of integration of AEM and ReactJs
====================

This is an example integration of AEM and ReactJS 


### Requirements

- AEM 6.2


### Base Component
aemcomponent.js

    export default (WrappedComponent) => {
          class AemComponent extends WrappedComponent {
              parseComponentName(dataPath){
                  return dataPath.substring(dataPath.lastIndexOf('/') + 1);
              }
      
              generateCqWrapper(dataPath, resourceType) {
                  let dialogData = {
                      'path':dataPath,
                      'slingPath':dataPath + '.html',
                      'dialog':'/apps/' + resourceType + '/cq:dialog',
                      'dialogLoadingMode':'auto',
                      'dialogLayout':'auto',
                      'dialogSrc':'/mnt/override/apps/' + resourceType + '/_cq_dialog.html/' + dataPath,
                      'type':resourceType,
                      'csp':'partial|page/' + this.parseComponentName(dataPath)};
      
                  let wrapper = {
                      'data-path': dataPath,
                      'data-config':JSON.stringify(dialogData).replace(/["]/g, "'")
                  };
                  return wrapper;
              }
      
              render () {
                  const elementsTree = super.render();
                  if (window.__env.appMode === 'author' && this.state.cqProp.dataPath !== undefined) {
                      let cqElement = React.createElement('cq', this.generateCqWrapper(this.state.cqProp.dataPath,this.state.cqProp.resourceType), null);
                      let cqWrapper = Object.assign({}, elementsTree.props.children, cqElement);
                      let childArray = React.Children.toArray(elementsTree.props.children);
                      childArray.push(cqWrapper);
                      return React.cloneElement(elementsTree, elementsTree.props, childArray);
                  } else {
                      return elementsTree;
                  }
      
      
              }
          }
          return AemComponent;
      } 


This component can wrapper the other normal reactjs component



### Example
If you want to warpper component 'TopTabs'

    TopTabs = aemWrapper(TopTabs);
    export default TopTabs; 




