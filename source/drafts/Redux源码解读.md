### Redux
#### createStore

#### combineReducers
#### bindActionCreators
#### applyMiddleware
#### compose

### React-Redux
#### Provider
#### connect

```javascript    

	const defaultMapStateToProps = state => ({}) // eslint-disable-line no-unused-vars
	const defaultMapDispatchToProps = dispatch => ({ dispatch })
	const defaultMergeProps = (stateProps, dispatchProps, parentProps) => ({
	  ...parentProps,
	  ...stateProps,
	  ...dispatchProps
	})

```
